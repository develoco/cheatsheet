ghpages, jekyll, githubpages

# Sample configs

`_config.yml`

```
theme: jekyll-theme-cayman
title: Diego Lavalle
description: Low-key software development
show_downloads: false
include: [".well-known"]
github:
  repository_url: https://github.com/diegolavalle
```

```
theme: jekyll-theme-leap-day
title: HyperHooks
description: A new experimental approach to Hyperscript and hooks
show_downloads: true
github:
  zip_url: https://github.com/hyperhooks/hyperhooks/archive/master.zip
  tar_url: https://github.com/hyperhooks/hyperhooks/archive/master.tar.gz
  repository_url: https://github.com/hyperhooks/hyperhooks
```

# Remote themes

Have a repository somewhere with assets, includes, sass/css and layouts. Use it in a different project by adding `remote_theme:` in your `_config.yml`:

```yaml
remote_theme: maincc/sometheme

# No longer use theme
# theme: jekyll-theme-builtin

# There is a bug with the following
# github:
#  repository_url: https://github.com/diegolavalle
```

**Do NOT include a `github` directive in your _config.yml_ together with `remote_thene`.** There is a bug as of September 2019 that causes for the remote theme not to work. 

# Custom domains

Add `CNAME` file to your GH Pages branch containing a single line with your domain name:

```
diegolavalle.com
```

The A record on your DNS has to point to the right GitHub IP

# Tricks

To prevent Jekyll from processing anything in your GH Pages branch add an empty `.nojekyll` file to your repo.

## Subfolders

For organizations and users, sub-repos act as folders for instance.

`diegolavalle.com/some-repo`

or

`yourname.github.io/your-project`

These can have a different GH Pages setting / Jekyll configuration

# Running locally

Use docker (recommended) or the GH pages gem
