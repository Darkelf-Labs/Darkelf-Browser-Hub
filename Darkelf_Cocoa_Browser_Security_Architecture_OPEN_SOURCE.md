# Darkelf Cocoa Browser -- Security Architecture & Protective Features

## Overview

Darkelf Cocoa Browser is built with a hardened runtime philosophy,
integrating privacy-first browsing, runtime isolation, and the **Darkelf
MiniAI Sentinel** open-source real-time monitoring engine.

This document outlines all active security mechanisms and protective
layers.

------------------------------------------------------------------------

# 1. Runtime & Process Hardening

## Hardened Runtime

-   Enforced strict Content Security Policy (CSP) on internal pages
-   No inline scripts or external JavaScript execution on internal
    console pages (`script-src 'none'`)
-   `frame-ancestors 'none'` prevents clickjacking
-   `object-src 'none'` blocks plugin/object embedding
-   `form-action 'none'` prevents data exfiltration via forms
-   `connect-src 'none'` prevents XHR/fetch/WebSocket connections

## No Persistent Web Data (Threat Console)

-   Uses `WKWebsiteDataStore.nonPersistentDataStore()`
-   Prevents cookies, storage, and tracking persistence
-   Session-based memory only

------------------------------------------------------------------------

# 2. Darkelf MiniAI Sentinel

The **Darkelf MiniAI Sentinel** is an open-source real-time threat
classification and monitoring engine embedded directly into the browser
runtime.

It continuously analyzes browser activity and network behavior.

## Session Metrics Collected

-   Session uptime
-   Total network events
-   Unique domains visited
-   Events per minute (activity rate)
-   Lockdown status

## Threat Categories Tracked

-   Trackers
-   Intrusions
-   Malware attempts
-   Exploits
-   Fingerprinting attempts
-   HTTP blocks

The engine provides live internal telemetry to the Threat Console without
executing any external scripts to the user.

------------------------------------------------------------------------

# 3. Blocking & Detection Systems

## Tracker Blocking

Detects and blocks known tracking domains and behavioral fingerprinting
attempts.

## Fingerprinting Detection

Monitors: - Canvas fingerprint attempts - WebGL probing - Browser
entropy harvesting

## HTTP Block System

Prevents suspicious outbound requests based on policy rules.

## Lockdown Mode

When activated: - Aggressive blocking policies - Reduced network surface
area - Elevated runtime restrictions

------------------------------------------------------------------------

# 4. Threat Report Console Security

The Threat Console is sandboxed and tightly restricted.

### Execution Controls

-   Strict Content Security Policy (CSP)
-   No JavaScript execution (`script-src 'none'`)
-   No embedded objects (`object-src 'none'`)
-   No form submission (`form-action 'none'`)
-   No framing allowed (`frame-ancestors 'none'`)
-   No navigation away from the console (`navigate-to 'none'` where
    supported)

### External Resource Policy

-   No third-party **scripts**
-   No external API connections (`connect-src 'none'`)
-   Limited external resource loading:
    -   Bootstrap Icons CSS and fonts from `https://cdn.jsdelivr.net`
    -   Explicitly allowed via `style-src` and `font-src` CSP directives

This means the Threat Console performs a controlled outbound HTTPS
request for icon styling but executes **no third-party code**.

------------------------------------------------------------------------

# 5. Address Bar Security Indicator

Dynamic trust signaling at the native UI layer:

-   Green Lock: Trusted HTTPS connection
-   Warning Indicator: Suspicious or untrusted state

Rendered using AppKit components, preventing DOM-level spoofing.

------------------------------------------------------------------------

# 6. Navigation & Internal Route Isolation

Internal routes (e.g., `darkelf://report`) are virtualized and
regenerated manually.

Benefits:

-   No URL-based injection
-   No reload-based exploitation for internal routes
-   Manual HTML regeneration for system pages
-   Isolation from external navigation stack

------------------------------------------------------------------------

# 7. Privacy Protections

-   No referrer leakage (`<meta name="referrer" content="no-referrer">`)
-   No external script loading
-   No external API calls from internal pages
-   No persistent storage for system console
-   Strict image source controls (`img-src 'self' data:`)

------------------------------------------------------------------------

# 8. Blocking Effectiveness Metric

Calculated as:

    (trackers + fingerprinting) / total_events * 100

This measures detection effectiveness --- not vulnerability.

Higher values indicate successful interception of tracking-related
activity in tracker-heavy environments.

------------------------------------------------------------------------

# 9. UI Isolation Strategy

-   Internal UI rendered separately from browsing context
-   No shared script execution between console and web content
-   Security indicator rendered at native layer (AppKit level)

------------------------------------------------------------------------

# 10. Hardened Design Philosophy

Darkelf Cocoa Browser operates on:

-   Zero-trust assumptions
-   Minimal attack surface
-   Open-source runtime intelligence (Darkelf MiniAI Sentinel)
-   Strict internal sandboxing
-   Explicit reload handling for virtual routes
-   Controlled and explicit external resource allowances

------------------------------------------------------------------------

# Summary

Darkelf Cocoa Browser implements:

-   Runtime sandboxing
-   Tracker & fingerprint detection 
-   Canvas Randomization
-   No WebRTC
-   Multiple Spoofs
-   Open-source Darkelf MiniAI Sentinel threat engine
-   Lockdown enforcement
-   Strict CSP for internal tools
-   Non-persistent data stores
-   Navigation isolation
-   Native-level security indicators
-   Controlled third-party CSS/font allowance (no third-party script
    execution)

This layered defense model ensures modern web compatibility while
minimizing tracking, fingerprinting, and exploitation risk.
