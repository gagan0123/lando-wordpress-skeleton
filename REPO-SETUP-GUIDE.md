# Repo Setup Guide

## Creating repo with wordpress-skeleton template

![WordPress-Skeleton Template for repo creation](https://user-images.githubusercontent.com/25586785/70203131-efb2b180-1741-11ea-9bb4-3e7790bf3832.png)

## Setting up the Lando local environment

`.lando.yml` is shared by every developer, so after creating a repo from the skeleton:

1. Change `name:` to the project slug. The name keys the container names, the database volume and the `*.lndo.site` hostname, so two checkouts sharing a name share one database. Update the `proxy:` hostname for Mailpit and the `WP_REDIS_PREFIX` environment value to match.
2. Review the pinned versions: `php`, `database`, `WP_VERSION` and the Node.js major in `build_as_root`. Match production where it exists.
3. Run `lando start`. The first run prints a generated admin password for the `rtcamp` user. To use a fixed password instead, create a gitignored `.lando.local.yml`:

   ```yaml
   services:
     appserver:
       overrides:
         environment:
           WP_ADMIN_PASSWORD: your-local-password
   ```

   Reset it later with `lando wp user update rtcamp --user_pass=<new>`.

Useful commands:

| Command | Purpose |
|---|---|
| `lando wp ...` | WP-CLI inside the appserver |
| `lando phpcs` / `lando phpcbf` | PHP_CodeSniffer with the repo `phpcs.xml` |
| `lando xdebug <mode>` | Toggle Xdebug (`debug`, `profile`, `off`, ...) without a rebuild |
| `lando db-import <file>` | Import a dump; `post-db-import` events flush caches and rewrites |
| `lando npm ...` / `lando node ...` | Node.js inside the appserver |

Mailpit catches all outgoing mail at `https://<name>-mail.lndo.site`. Uploads missing locally return 404; to pull them from production instead, edit the `@prod_uploads` block in `.lando/nginx.conf`.

## Adding description and what to add in it

GitHub repo description should have a short brief about the repo and site url if applicable.
![GitHub repo description and url](https://user-images.githubusercontent.com/42698168/70128800-8c724200-16a3-11ea-9c21-57c408cb86ec.png)

Example Description: A completely new look and branding of example.com along with Gutenberg, AMP, PWA, a PDF generator for case study.
Example Url: example.com

## Managing topics and what to add in topics

GitHub repo topics tags should contain the following:
1. Describe the technologies used in the project. (PHP, node, etc.)
2. Describe the hosting service and EasyEngine version if used. (AWS, EE4 | VIP-GO, etc.)

Guide on how to add topics: https://help.github.com/en/github/administering-a-repository/classifying-your-repository-with-topics

## Setting up branch protection rules

Branch protection rules should be added to the main branches of the project. It is also necessary to add branch protection rules when adding branch delete mechanism by default as described below, so that these default and main branches are not deleted. 

A good startpoint is to protect `main`, `staging`, `develop`, `qa`, `testing`.
Any branch other than protected branch should be created according to branch naming guidelines given in project readme.

Guide to enable branch protection: https://help.github.com/en/enterprise/2.16/admin/developer-workflow/configuring-protected-branches-and-required-status-checks#enabling-a-protected-branch-for-a-repository

## Setting up deletion of merged branches

Branches other than protected branches should be removed as soon as their work is done. To setup automatic deletion of branches when they are merged go through the below guide. Please make sure to protect branches before setting this up. Otherwise it can lead to deletion of required branches.

Guide to setup auto deletion of merged branches: https://help.github.com/en/github/administering-a-repository/managing-the-automatic-deletion-of-branches

Please note: This will delete any branch other than protected branches that is merged via PR against any branch. If you want to delete branches that should deleted only if a PR is merged against `main`. Then that will be a seprate integration. Contact sys team for it until it's setup instructions are added here.

## How to configure and tweak GitHub actions

You need to add `VAULT_ADDR` and `VAULT_TOKEN` secrets to GitHub repository. Contact sys team if you don’t have these.

1. `.github/workflows/deploy_on_push.yml` - Update `WP_VERSION` according to WordPress version required in project and `SLACK_CHANNEL` to channel for deployment notifications.
2. `.github/workflows/plugin_update_every_week.yml` - By default this will raise PRs against develop branch when plugin update is found. If you want to change the branch, update `develop` in the file at both places with required file name.

For more details, refer:
https://github.com/rtCamp/action-deploy-wordpress/
https://github.com/rtCamp/action-plugin-update/

## Setting up slack notifications for the repo

GitHub slack integration is present in rtCamp slack. 

Guide to subscribe for GitHub notifications in slack channel: https://github.com/integrations/slack#subscribing-and-unsubscribing/

Ideally create a bot channel to subscribe the GitHub notifications of the project.

## Setting up labels for the repo

Guide: https://help.github.com/en/github/managing-your-work-on-github/about-labels

## Setting up depedabot

Dependabot has been installed rtCamp organization wide.

For your repo, if you are using php-composer or javascript or submodule packages, you can create dependabot config file so that it knows which dependencies to check for.

Guide to create config file: https://dependabot.com/docs/config-file/#dependabot-config-files

In case you want to update settings for dependabot or face any issues you can ping sys team.
