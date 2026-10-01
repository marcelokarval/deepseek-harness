# Agent Note: Skip browser signing material when browser auth is disabled

Status: implemented

English | [中文](2026-09-05-optional-browser-auth-skips-signing-grant.zh.md)

## Problem

The private-LAN Web composition disables `requireBrowserAuth`, but Connection still created BrowserAuth during plugin activation. That wrote a persistent browser-session signing grant even though the process token and signed cookie paths were unreachable.

## Decision

Connection creates BrowserAuth only when `requireBrowserAuth` resolves to `true`. The default remains enabled. When the authorized LAN composition sets it to `false`, HostConnectionService omits launch-token URLs and accepts trusted requests without a browser-session cookie; the existing Host/Origin trust fence still rejects untrusted requests.

## Alternatives considered

**Keep the unused signing grant.** Retaining a credential that no request can use enlarges the credential footprint and leaves future operators to rotate irrelevant state.

**Disable the Host/Origin trust fence with browser auth.** Browser-session authentication and authority validation protect different properties. The LAN composition omits only the browser exchange.

**Change the default to disabled.** Other profiles require the existing token and cookie flow, so the explicit deployment setting remains the only opt-out.

## Consequences

The disabled configuration no longer persists signing material. Focused node-half tests first observed one credential write, then prove zero writes with disabled auth, trusted-request passage, and untrusted-request rejection. The existing enabled-auth tests remain in the same suite.
