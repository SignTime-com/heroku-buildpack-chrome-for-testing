# Heroku Buildpack, Chrome for Testing

This buildpack installs `chrome-headless-shell` & [`chromedriver`](https://chromedriver.chromium.org/), the Selenium driver for Chrome, in a Heroku app.

## Background

In summer 2023, the Chrome development team [addressed a long-standing problem with keeping Chrome & Chromedriver versions updated and aligned](https://developer.chrome.com/blog/chrome-for-testing/) with each other for automated testing environments. This buildpack follows this strategy to keep `chrome` & `chromedriver` versions  in-sync.

## Installation

> [!IMPORTANT]
> If migrating from a previous Chrome-chromedriver installation, then remove any pre-existing Chrome or Chromedriver buildpacks from the app. See the [migration guide](#migrating-from-separate-buildpacks).

```bash
heroku buildpacks:add -i 1 https://github.com/SignTime-com/heroku-buildpack-chrome-for-testing
```

Deploy the app to install Chrome for Testing. 🚀 

## Selecting the Chrome Release Channel

By default, this buildpack will download the latest `Stable` release, which is provided
by [Google](https://googlechromelabs.github.io/chrome-for-testing/).

You can control the channel of the release by setting the `GOOGLE_CHROME_CHANNEL`
config variable to `Stable`, `Beta`, `Dev`, or `Canary`, and then deploy/build the app.

## Migrating from Separate Buildpacks

### Remove Existing Installations

When an app already uses the separate Chrome & Chromedriver buildpacks, remove them from the app, before adding this one:

```
heroku buildpacks:remove heroku/google-chrome
heroku buildpacks:remove heroku/chromedriver

heroku buildpacks:add -i 1 https://github.com/SignTime-com/heroku-buildpack-chrome-for-testing
```

### Path to Installed Executables

After being installed by this buildpack, `chrome-headless-shell` & `chromedriver` are set in the `PATH` of dynos.

If the absolute paths are required, you can discover their location in an app:

```bash
>>> heroku run bash
$ which chrome-headless-shell
/app/.chrome-for-testing/chrome-headless-shell-linux64/chrome-headless-shell
$ which chromedriver
/app/.chrome-for-testing/chromedriver-linux64/chromedriver
```

These locations may change in future versions of this buildpack, so please allow the operating system to resolve their locations from `PATH`, if possible.

### Changes to Command Flags

Depending on how an app is already setup for testing with Chrome, it may not require any changes.

**If the app fails to start Chrome**, please ensure that the following argument flags are set wherever `chrome` is invoked:

* `--headless`
* `--no-sandbox`

Some use-cases may require these flags too:

* `--disable-gpu`
* `--remote-debugging-port=9222`
