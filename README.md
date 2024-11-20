# windows-authenticode-cert-tagging

Standalone `certificate_tag` tool from https://github.com/google/omaha to modify signed Windows PE and MSI binaries without breaking the signature.

The code is based on a plain copy of the following revision: https://github.com/google/omaha/tree/c3e428cce2af4f8619658b553292147643820219/common/certificate_tag
Some details about the tool's context can be read at the Omaha overview at https://omaha-consulting.com/google-omaha-tutorial-chrome-updater.

The `certificate_tag` tool supports two modes:

1. putting content into the Authenticode signature after the PKCS#7 blob
2. using a "superfluous certificate"

The first mode might not work, if Windows is configured with an enabled `EnableCertPaddingCheck`. See https://learn.microsoft.com/en-us/security-updates/securityadvisories/2014/2915720 for details. The check is opt-in only, so we can assume that the first mode should work on most systems.

## Install

Release artifacts for all popular platforms can be downloaded from https://github.com/Scopevisio/windows-authenticode-cert-tagging/releases/latest.

A recent Golang version is required if you want to install from source:
```shell
# You should use a specific release like "v0.2.0" instead of "latest".
go install github.com/Scopevisio/windows-authenticode-cert-tagging/cmd/certificate_tag@latest
```

## Usage

```shell
certificate_tag --help
```

---

Original readme follows below

---

certificate_tag.go is a tool for manipulating "tags" in Authenticode-signed,
Windows binaries.

Traditionally we have inserted tag data after the PKCS#7 blob in the file
(called an "appended tag" here). This area is not hashed in when checking
the signature so we can alter it at serving time without invalidating the
Authenticode signature.

However, Microsoft are changing the verification function to forbid that so
this tool also handles "superfluous certificate" tags. These are dummy
certificates, inserted into the PKCS#7 certificate chain, that can contain
arbitrary data in extensions. Since they are also not hashed when verifying
signatures, that data can also be changed without invalidating it.

More details are here: http://b/12236017

The tool was updated in 2020 to support MSI files: b/172261939, b/165818147.

The test file is integrated from google3, but is modified here to make it
easier to run outside of google3; see the comment near the beginning of
certificate_tag_test.go

---

# Omaha

## This is not an official Google product.

Omaha is the open-source version of Google Update, a program to install requested software and keep it up to date.  The Google-branded version of Omaha is used to support software patching (both background updating, and on-demand update checks) for Google Chrome, Earth, and a variety of other Google products on Windows 7, 8, and 10.

For a quick overview of how Omaha works, you can see [this unofficial tutorial](https://fman.io/blog/google-omaha-tutorial/). Please note that it was written by a third party so we cannot guarantee its availability, accuracy or safety.

We know that keeping software updated is both important and hard, and so by open-sourcing this project, our hope is that perhaps we can help others solve this problem. So, if you'd like to get involved, or even use Omaha to support your own software projects, then just follow the instructions in the [Developer Setup Guide](https://github.com/google/omaha/blob/master/doc/DeveloperSetupGuide.md), and you'll be good to go!
