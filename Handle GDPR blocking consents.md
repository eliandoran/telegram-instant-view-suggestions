#  _Suggestion_: Handle GDPR blocking consents

On most websites, consenting for GDPR is done via a non-intrusive pop-up. However, it seems that there are also websites which instead of displaying a pop-up show a different page entirely, rendering Instant View helpless.

An example of a website having GDPR consents breaking IV is [health.com](health.com). They seem to have used a third-party service for handling GDPR called _OneTrust Privacy Portal_. Upon entering the website for the first time (on any device and in IV), the user is greeted by a page of consent. The page that was supposed to be retrieved from the server is replaced entirely, making any template unable to retrieve the page data.

Considering that otherwise the websites having this GDPR issue would be perfect candidates for Instant View, I believe the team at Telegram should consider coming up with a solution to this issue.

This should not cause any privacy concerns for European users, since the rules of the website regarding cookies are no longer applicable due to the websites being rendered and processed on Telegram's own servers.

# Suggestions of implementations
## a) Allow the template to render the page with a different locale
One of the most versatile solution to this issue would actually be to allow the template author to switch the country/region that would be reported to the website. For example, assuming a region from United States would not cause GDPR notices on most websites.

Since the introduction of _Options_, I believe something similar to this would go very well with this solution:

```
~version: "2.0"
~locale: "en-US"
```

_or:_

```
~version: "2.0"
~region: "United States"
```

## b) Allow the template to spoof the user agent

Another truly versatile solution would be to allow the template author to choose a different user agent.

Websites must not render the GDPR blocking popup for crawlers such as Google because that would severly impact their ratings (at the same time rendering different content for crawlers is frowned-upon). This is one of the reasons user agent spoofing might work.

I believe the template author should not be able to choose the user agent string, but rather choose from a preselected set of choices (Google and whatever crawlers exist nowadays). This is due to the fact that a malevolent user could be able to determine precisely which IPs are used by the Telegram bot by probing the user agent (unless you already expose this information).

A simple variant would be:
```
~useragent: google-bot
```

## c) Implement vendor-specific GDPR anti-measures
For example, as mentioned previously, [health.com](health.com) uses _OneTrust Privacy Portal_.

Doing a short analysis, it seems that the GDPR consent can be easily bypassed by using generating the following cookie for the target domain:
```
euConsent: true
```

In turn, this can be done in multiple ways:

1. Make the Instant View processing algorithm automatically look for signs of a GDPR consent and generate the appropriate cookie (most probably all consent solutions are cookie-based).
2. Have Instant View generate by default all known cookies for GDPR consents, simplifying development time significantly.
3. Implement anti-measures for well-known consent solutions and have them manually turned on by template authors via something like `~gdpr: onetrust`.
4. The most powerful (and possibly unsafe?) method would be to allow template authors to generate cookies on behalf of the Instant View crawler, via something like `@cookie("euConsent", "true")` or `~cookie: "euConsent: true"`.
