# Link app to website for credentials autofill

## Find your Team ID

Log in to _Apple's Developer Center_
Once you are logged in, click on _Account_, then _Membership_
Under Membership Information you will find the _Team ID_ field
It should look something like `D3KQX62K1A`

## Create an app with an App ID

Create a dummy app with a nice bundle identifier (this will become the App ID)
Set the signing to your ADC team

To set up the entitlement in your app, open the project’s Capabilities tab and enable Associated Domains. This step adds the Associated Domains Entitlement to your app as well as the associated domains feature to your app ID.

To add your domain to the entitlement, click Add (+) at the bottom of the Domains table in order to add a placeholder domain with the webcredentials: prefix. Replace the placeholder with your site’s domain, retaining the prefix.

Each domain you specify uses the following format:
`<service>:<fully qualified domain>[:port number]`

So in this case: `webcredentials:diegolavalle.com`

`*` is allowed to match subdomains. Other services include `applinks` and `activitycontinuation`

More info: https://developer.apple.com/documentation/security/password_autofill/setting_up_an_app_s_associated_domains

## Add verification file to your domain's root

Upload the following file to:
`https://diegolavalle.com/.well-known/apple-app-site-association`

```
{
  "webcredentials": {
    "apps": ["D3KQX62K1A.com.diegolavalle.dlsupport"]
  }
}
```

Note: the file has to remain accessible by the app **always**

### GitHub pages with Jekyll

Make Jekyll explicitly include the `.well-known` hidden subdirectory by adding this to `_config.yml`:

    include: [".well-known"]


# Verify the domain for _Sign In with Apple_

Add a DNS TXT SPF record to your domain.

In GoDaddy (with GoDaddy email service) the value should be `v=spf1 a mx include:secureserver.net ~all` with `@` for domain (unless configuring for subdomains) and default `TTL` of 1 hour.

Verify in Apple Developer Connection and download the `apple-developer-domain-association.txt` file to add to your site's root

Upload the domain association file to your `.well-known` folder (include the folder in GH Pages with Jekyll enabled) `https://diegolavalle.com/.well-known/apple-developer-domain-association.txt`

This will allow you to contact users with Apple’s private email relay service

