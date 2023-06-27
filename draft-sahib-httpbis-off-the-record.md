---
title: "Off-The-Record HTTP Header"
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
  latest: "https://brave-experiments.github.io/draft-sahib-httpbis-off-the-record/draft-sahib-httpbis-off-the-record.html"

author:
 -
    fullname: Your Name Here
    organization: Your Organization Here
    email: "shivankaulsahib@gmail.com"

normative:

informative:



--- abstract

This document introduces an HTTP response header field that allows a server to signal to the client that the requested website should be considered "off-the-record." The purpose is to indicate that the server considers the content sensitive in some manner, and the client may prefer not to keep any evidence of accessing it.


--- middle

# Introduction

Browsers record information about users' browsing behavior and interests, both explicitly (e.g. browsing history, DOM storage, cookies) and implicitly (e.g. cache state, saved credentials, URL auto-complete). In situations where an attacker has physical access to the victim's device, this information constitutes a privacy leak and can be used for surveillance. This kind of physical access is especially common in cases of intimate partner violence {{?IPV=I-D.celi-irtf-hrpc-ipvc}}. Client software currently provide some tools to help users hide their activity on sensitive sites, such as incognito/private mode or the ability to edit browsing history. However, these tools are insufficient to protect people whose safety depends on it: they either hide too much (thus inviting suspicion from abusers), too little (thus allowing abusers to recover browsing history), or are otherwise difficult to use successfully in a stressful situation.

The `Request-OTR` HTTP response header allows websites to optionally describe their own content as "sensitive." The client can then choose to not record the site visit and remove evidence of the site visit by preventing state from persisting to disk (such as {{?COOKIES=I-D.ietf-httpbis-rfc6265bis}}).


# Conventions and Definitions

{::boilerplate bcp14-tagged}

* `sensitive`: TODO.

# Motivation

## Comparisons With Other Client-Side Approaches

### Private Browsing

### Manual Editing

### Clear-Site-Data

# Definition (?)

Structured Field Value

# Deployment Considerations

## User Experience

## Proxies

# Security Considerations

## Navigation History

## Malicious Websites

## Consent





# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
