---
title: "resource: URLs"
short-title: "resource:"
slug: Web/URI/Reference/Schemes/resource
page-type: uri-scheme
sidebar: urlsidebar
---

{{non-standard_header}}

Resource URLs, URLs prefixed with the `resource:` scheme, are used by
Firefox and Firefox browser extensions to load resources internally, but some of the
information is available to sites the browser connects to as well.

## Syntax

```url
resource://<path>
```

- `resource:`
  - : The scheme of the URL.
- `<path>`
  - : A path pointing to the resource you want to load.

An example:

```url
resource://gre/res/svg.css
```

When arrows are found in the resource URL's ('->'), it means that the first file
loaded the next one:

```url
resource://<File-loader> -> <File-loaded>
```

Please refer to [the URI reference](/en-US/docs/Web/URI) for more general details.

In this article, we focus on resource URLs, which are used internally by Firefox to
point to built-in resources.

## Threats

Because some of the information shared by `resource:` URLs is available to
websites, a web page could run internal scripts and inspect internal resources of
Firefox, including the default preferences, which could be a serious security and
privacy issue.

For example, [a script on Browserleaks](https://browserleaks.com/resource-urls) highlights what Firefox reveals when queried by a script
running on the site (you can find the code in <https://browserleaks.com/resource-urls#more>).

The file firefox.js passes preference names and values to the pref() function. For
example:

```url
http://searchfox.org/mozilla-central/rev/48ea452803907f2575d81021e8678634e8067fc2/browser/app/profile/firefox.js#575
```

Websites can easily collect Firefox default preferences by overriding this
`pref()` function and using the script
`resource:///defaults/preferences/firefox.js`.

Furthermore, some default values of preferences differ between build configurations,
such as platform and locale, which means websites could identify individual users using
this information.

## Solution

In order to fix this problem, Mozilla changed the behavior of loading `resource:` URLs in
[Firefox bug 863246](https://bugzil.la/863246), which landed in [Firefox 57 (Quantum)](/en-US/docs/Mozilla/Firefox/Releases/57).

In the past, web content was able to access whatever `resource:` URLs were
desired — not only Firefox's internal resources, but also extensions' assets. Now this
behavior is prohibited by default.

It is however still necessary for Firefox to load resources in web content under
certain circumstances. For example, if you open the view source page (View Page Source
or View Selection Source), you will find it requires `viewsource.css` through
a `resource:` URL. Resources that have to be exposed to web content have
been moved to a new location named `resource://content-accessible/`, which is
isolated and only contains non-sensitive resources. In this way we can keep essential
resources exposed and have most threats eliminated.

> [!NOTE]
> It is recommended that web and extension developers don't try
> to use resource URLs anymore. Their usage was hacky at best, and most usage won't work
> any more.

## Specifications

resource: is not defined in any specification.

## Browser compatibility

resource: is Firefox only.

## See also

- [URIs](/en-US/docs/Web/URI)
- [What is a URL?](/en-US/docs/Learn_web_development/Howto/Web_mechanics/What_is_a_URL)
- [IANA list of URI schemes](https://www.iana.org/assignments/uri-schemes/uri-schemes.xhtml) (`resource:` is [covered here](https://www.iana.org/assignments/uri-schemes/prov/resource))
