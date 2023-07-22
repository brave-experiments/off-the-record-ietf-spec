---
title: "The Off-The-Record Response Header Field"
abbrev: "OTR"
category: std

docname: draft-sahib-httpbis-off-the-record-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "HTTP"
venue:
  group: "HTTP"
  type: "Working Group"
  mail: "ietf-http-wg@w3.org"
  arch: "https://lists.w3.org/Archives/Public/ietf-http-wg/"
  github: "brave-experiments/draft-sahib-httpbis-off-the-record"
  latest: "https://brave-experiments.github.io/draft-sahib-httpbis-off-the-record/#go.draft-sahib-httpbis-off-the-record.html"

author:
 -
    fullname: Shivan Kaul Sahib
    organization: Brave Software
    email: "shivankaulsahib@gmail.com"

normative:

informative:

  FREED_ET_AL:
    title: "“A Stalker’s Paradise”: How Intimate Partner Abusers Exploit Technology"
    date: 2018
    target: "https://dl.acm.org/doi/pdf/10.1145/3173574.3174241"
    author:
      - ins: D. Freed
      - ins: J. Palmer
      - ins: D. Minchala
      - ins: K. Levy
      - ins: T. Ristenpart
      - ins: N. Dell

  PSL:
    title: "Public Suffix List"
    target: "https://publicsuffix.org/"
    author:
      - ins: Mozilla

  CLEAR_SITE_DATA:
    title: "Clear Site Data - W3C Working Draft, 30 November 2017"
    target: "https://www.w3.org/TR/clear-site-data/"
    author:
      - ins: M. West



--- abstract

This document specifies an HTTP response header field that enables a server to inform the client that the requested website should be treated as "off-the-record." The purpose is to indicate that the server considers the content sensitive in some way, and the client should not retain any record of accessing it.


--- middle

# Introduction

Browsers record information about users' browsing behavior and interests, both explicitly (e.g. browsing history, DOM storage, cookies) and implicitly (e.g. cache state, saved credentials, URL auto-complete). In situations where an attacker has physical access to the victim's device, this information constitutes a privacy leak and can be used for surveillance. This kind of physical access is especially common in cases of intimate partner violence {{?IPV=I-D.celi-irtf-hrpc-ipvc}}. Client software currently provide some tools to help users hide their activity on sensitive sites, such as incognito/private mode or the ability to edit browsing history. However, these tools are insufficient to protect people whose safety depends on it: they either hide too much (thus inviting suspicion from abusers), too little (thus allowing abusers to recover browsing history), or are otherwise difficult to use successfully in a stressful situation.

The `Request-OTR` HTTP response header described in this document allows websites to classify their own content as "sensitive" and request to be treated as "off-the-record." The client can then choose to not record the site visit and remove evidence of the site visit by preventing persistent storage of related data to disk (such as {{?COOKIES=I-D.ietf-httpbis-rfc6265bis}}). See {{comparison}} for a comparison with other approaches a client can take to remove evidence of accessing a sensitive website.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The following terminology is used throughout the document:

* `UI-bound adversary`: An attacker who has authenticated access to a victim's device via standard user interfaces {{FREED_ET_AL}}.

* `registered domain`: Also known as "effective top level domain + 1" or "eTLD+1", an origin's "registered domain" is the origin's host's public suffix plus the label to its left, with the term "public suffix" being defined in a note in {{?RFC6265, Section 5.3}} as "a domain that is controlled by a public registry" {{PSL}}.

* `sensitive`: This document uses "sensitive" content to mean anything that a website thinks is worth hiding from a UI-bound adversary. Websites self-report their content as sensitive.

* `client`: Software acting on behalf of users, typically a Web browser.

* `Off-the-record (OTR)`: Client operation mode where the client tries to not record or persist state or evidence of having visited a a particular site (see {{otr-client-mode}}).

* `OTR session`: A client is said to be in an OTR session for a site when it is treating interactions with the site as being off-the-record. The scope of an OTR session is the site's registered domain.

This document uses the following terminology from Section 3 of {{!RFC8941}} to specify syntax and parsing: Boolean.

# Request-OTR Response Header {#header}

## Definition

Request-OTR is an Item Structured Header {{!RFC8941}}. Its value MUST be a Boolean {{RFC8941, Section 3.3.6}}. Its ABNF is:

~~~
Request-OTR: sf-boolean
~~~

For example:

~~~
Request-OTR: ?1
~~~

## Operation

The Request-OTR response header field is used to indicate that the client SHOULD treat the site as sensitive and not keep traces of the user having visited the site. See {{otr-client-mode}} for details on considerations and protections a client can use in an OTR session.

The scope of the OTR session is defined as the requesting site's registered domain. Sites request an Off-The-Record session by including the `Request-OTR` header in the initial navigation request. On receiving this header, a client MAY ask for the user's consent before initiating an OTR session for the site (see {{consent}}).

A header field value of `?0` i.e. false is treated as if the header was not present.

<!-- TODO: can a site opt-out of OTR mode? how does it stop being in OTR mode? -->

<!-- TODO: If the client is already in an OTR session for the site when it receives the `Request-OTR` header for the same site, the client SHOULD use the same session; optionally, a client MAY create a new OTR session for a site after a timeout.
 -->


# Off-The-Record Session {#otr-client-mode}

The main purpose of an OTR session is to not persist the user's interactions with the site. A client can apply a number of protections and mitigations in order to achieve this:

1. Construct a new, empty, temporary storage area for the site for explicit (cookies, localStorage) and implicit storage (caches, autocomplete) attached to the OTR session. Every site in OTR mode should get its own temporary storage.
2. Prevent browser extensions from running in the OTR session.
3. Users are notified before they navigate away from the site (and thus away from the OTR session).


# Comparisons With Other Client-Side Approaches {#comparison}

## Private Browsing

Many web browsers come with a private browsing mode, also known as incognito mode. Private windows enable users to browse the internet without their browsing activity being recorded locally. However, private browsing has limitations when it comes to protecting users from on-device surveillance. It is easy to forget to open a private window before visiting a site, especially when experiencing stress, resulting in the site visit being permanently recorded. Similarly, forgetting to close the private window may lead to unintended browsing in private mode beyond the target sensitive site. This can alert potential abusers to the use of private browsing, as the absence of browsing history may raise suspicion or put the victim at further risk.

## Manual Editing

Certain browsers provide advanced controls that allow users to manually delete browser storage for specific sites. This approach requires performing the deletion after visiting the site, rather than protecting the user during the visit. This can put the user at risk if the browser needs to be closed quickly. Furthermore, these controls are often challenging to locate and even more difficult for non-technical users to use correctly. Additionally, these browser controls typically only allow the user to delete specific stored data for the site, such as cookies or permissions, but do not provide the ability to remove other traces of the site, like browsing history or caches.

## Clear-Site-Data

`Clear-Site-Data` HTTP response header ({{CLEAR_SITE_DATA}}) lets websites ask a user agent to clear specific kinds of locally stored data. As noted in Section 6.1 of {{CLEAR_SITE_DATA}}, Clear-Site-Data acts after the fact, meaning the user agent retains data until the website requests its removal. In contrast, Request-OTR takes a preventative approach, where the client avoids storing data once it receives the header. Furthermore, with Clear-Site-Data, it is the website that defines which data should be cleared, not the client, which may leave the user exposed to identifying storage that the website may have overlooked. It's important to note that Clear-Site-Data does not provide a means to clear browser history; it only addresses web-visible storage.

<!-- # Deployment Considerations

## User Experience

## Proxies -->

# Security Considerations

## Navigation History

OTR mode only applies to the specific site requesting OTR mode. Notably, a user might have taken certain actions before getting to the sensitive site which would reveal the identity of the website. For example: a user looking for reproductive health centers near them would search for "reproductive health centers" on a search engine before navigating to a website that requests OTR mode; the browsing history would reveal the user's intentions. Request-OTR would only conceal activity on the site, not the navigation history leading up to it.

## Malicious Websites

Malicious websites could exacerbate harm by abusing this feature to hide traces of malicious activity. For instance, a malware website could use OTR mode as a means to conceal the download of malware onto the user's device.

## Consent {#consent}

To address the issue of malicious websites misusing OTR mode, one possible solution is to get the user's consent before enabling this mode. However, it's important to recognize that placing the responsibility of detecting and preventing abuse on clients can be challenging. Detecting malicious intent and ensuring appropriate consent can be complex tasks.

## Doesn't Protect Against the Website

OTR mode is not a privacy protection against the website operating in OTR mode. It simply treats the website as sensitive and prevents persistent storage of the site's contents on the client.

## Third Parties on Websites

Third-party trackers on websites services may still collect and retain data, even if the primary website is operating in OTR mode.

## Only Applicable for UI-bound Attackers

OTR mode is explicitly used to provide protection against UI-bound attackers who snoop local storage and browsing history. Sophisticated attackers could install local monitoring software on the device, or intercept and modify network traffic between the client and server, bypassing OTR mode's protections.

## Fingerprinting

A site MUST NOT be able to tell that a client is in OTR mode.

## Self-Identification as Sensitive

A censor could leverage this feature to conduct measurement studies aimed at identifying and subsequently banning websites that respond with the Request-OTR header.







# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

This document is based on work done by Mark Pilgrim, Sofía Celi, Pete Snyder and Shivan Kaul Sahib.
