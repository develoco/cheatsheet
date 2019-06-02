# Admin API

To consume pass the header
`Accept: application/vnd.github.v3+json`

## Sample usage

Install the your Admin GitHub app (for instance CmSandbox Admin)

Make sure to grant permission to a separate _content_ repository in the organization

To provide a link for installation use something like `https://github.com/apps/cmsandbox-admin/installations/new"`

Make note of your installation ID, you will need to pass this to the API client. This will also be passed as an URL query string param so you can capture it there.

As owner of the app generate a private key for your admin user `https://github.com/organizations/CmSandbox/settings/apps/cmsandbox-admin`

Both the installation ID and the private key can be stored in a keychain item or local storage for a web app to be able to use it.

## Crypto helper library

We need help from a javascript library to be able to sign requests with the private key

https://cdnjs.cloudflare.com/ajax/libs/jsrsasign/8.0.12/jsrsasign-all-min.js

## Javascript code

The following code authenticates, lists repos, saves and retrieves an independent git blob using a _ref_.

The file (blob) will not show on your content repo as it is not attached to any git tree

Capture the installation ID

```
const contentRef = 'someContent'
const getParameterByName = (nameArg) => {
    const url = window.location.href;
    const name = nameArg.replace(/[\[\]]/g, '\\$&');
    const regex = new RegExp('[?&]' + name + '(=([^&#]*)|&|#|$)')
    const results = regex.exec(url)
    if (!results) return null
    if (!results[2]) return ''
    return decodeURIComponent(results[2].replace(/\+/g, ' '))
}

let installationId = getParameterByName('installation_id');

if (installationId !== null) {
    localStorage.setItem('installationId', installationId)
}

installationId = localStorage.getItem('installationId')
if (installationId) {
    document.getElementById('installationId').value = installationId
} else {
    document.getElementById('status').textContent = 'GH app not installed/not properly configured'
}

```

Authenticate and get repo name

```
const authenticate = async () => {
    const now = Math.floor(Date.now() / 1000)
    const oHeader = {alg: 'RS256', typ: 'JWT'}
    let oPayload = {}
    oPayload.iss = '15907' // GitHub App's identifier
    oPayload.iat = now // issued at time
    oPayload.exp = now + 10 * 60 // JWT expiration time (10 minute maximum)
    const sHeader = JSON.stringify(oHeader)
    const sPayload = JSON.stringify(oPayload)

    const privateKey = localStorage.getItem('privateKey')
    const prvKey = KEYUTIL.getKey(privateKey)
    const sJWT = KJUR.jws.JWS.sign("RS256", sHeader, sPayload, prvKey)
    const response = await fetch("https://api.github.com/app", {
        headers: {
            "Authorization": `Bearer ${sJWT}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    const responseData = await response.json()
    console.log(responseData)

    const installationId = localStorage.getItem('installationId') // '279540'
    const responseInstall = await fetch(`https://api.github.com/installations/${installationId}/access_tokens`, {
        method: "post",
        headers: {
            "Authorization": `Bearer ${sJWT}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    const install = await responseInstall.json()
    console.log(install)

    installationToken = install.token
    const responseRepos = await fetch('https://api.github.com/installation/repositories', {
        headers: {
            Authorization: `token ${installationToken}`,
            Accept: 'application/vnd.github.machine-man-preview+json',
        },
    })
    const repoInfo = await responseRepos.json()
    console.log('Repository response', repoInfo)
    repo = repoInfo.repositories[0]
    refUrl = repo.git_refs_url.replace(/\{\/sha\}/g, `/tags/${contentRef}`)
    refsUrl = repo.git_refs_url.replace(/\{\/sha\}/g, '')
    blobsUrl = repo.blobs_url.replace(/\{\/sha\}/g, '')

    document.getElementById('repoFullName').textContent = repo.full_name
    document.getElementById('repoUrl').href = repo.git_url
    document.getElementById('repoUrl').textContent = repo.git_url
    document.getElementById('status').textContent = 'Private key ok'

}
```

Manage the private key storage:

```
const privateKey = localStorage.getItem('privateKey')
if (privateKey) {
    document.getElementById('privateKey').value = privateKey
    document.getElementById('status').textContent = 'Private key detected'
    authenticate()
} else if (installationId) {
    document.getElementById('status').textContent = 'Private key not configured'
}

document.getElementById('installationId').addEventListener('change', () => {
    localStorage.setItem('installationId', document.getElementById('installationId').value)
})

let installationToken = null
let repo = null
let refUrl = null
let refsUrl = null
let blobsUrl = null

document.getElementById('privateKey').addEventListener('change', async () => {
    localStorage.setItem('privateKey', document.getElementById('privateKey').value)
    authenticate()
})
```

Read the blob by ref

```
document.getElementById('readContent').addEventListener('click', async () => {
    const response = await fetch(refUrl, {
        headers: {
            "Authorization": `token ${installationToken}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    console.log('Ref response', response)
    if (response.status === 404) {
        console.log('Initialize the content first')
        const responseBlob = await fetch(blobsUrl, {
            method: 'post',
            body: JSON.stringify({
                content: 'Dummy content',
                encoding: 'utf-8'
            }),
            headers: {
                "Authorization": `token ${installationToken}`,
                "Accept": "application/vnd.github.machine-man-preview+json",
            },
        })
        const newBlob = await responseBlob.json()
        console.log('New blob', newBlob)
        const responseNewRef = await fetch(refsUrl, {
            method: 'post',
            body: JSON.stringify({
                ref: `refs/tags/${contentRef}`,
                sha: newBlob.sha,
            }),
            headers: {
                "Authorization": `token ${installationToken}`,
                "Accept": "application/vnd.github.machine-man-preview+json",
            },
        })
        const newRef = await responseNewRef.json()
        console.log('New ref', newRef)
    }
    const ref = await response.json()
    console.log(ref)

    const blobUrl = ref.object.url
    const responseBlob = await fetch(blobUrl, {
        headers: {
            "Authorization": `token ${installationToken}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    const content = await responseBlob.json()
    console.log(content)
    document.getElementById('customContent').value = atob(content.content)

})
```

Create/update the blob

```
document.getElementById('writeContent').addEventListener('click', async () => {
    const responseBlob = await fetch(blobsUrl, {
        method: 'post',
        body: JSON.stringify({
            content: document.getElementById('customContent').value,
            encoding: 'utf-8'
        }),
        headers: {
            "Authorization": `token ${installationToken}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    const newBlob = await responseBlob.json()
    console.log('New blob', newBlob)
    const response = await fetch(refUrl, {
        method: 'PATCH',
        body: JSON.stringify({
            sha: newBlob.sha,
            //force: true
        }),
        headers: {
            "Authorization": `token ${installationToken}`,
            "Accept": "application/vnd.github.machine-man-preview+json",
        },
    })
    const updatedRef = await response.json()
    console.log('Updated ref', updatedRef)
})
```
