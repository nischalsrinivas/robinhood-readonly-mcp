# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] - 2026-09-22

### Added
- Read-only option expiration, option-chain, and expiration/strike lookup tools
- stderr routing for `robin_stocks` output so MCP stdio remains valid
- Renamed public package and CLI branding to `robinhood-readonly-mcp`

## [0.1.2] (2026-03-08)


### Bug Fixes

* add cached single-symbol position lookup
* harden push approval auth flow

## [0.1.1] (2026-03-05)


### Bug Fixes

* correct type annotation for _safe_call func parameter

## [0.1.0] - 2026-01-06

### Added
- Initial release with 12 read-only tools
- Portfolio and positions tracking
- Stock quotes, fundamentals, and historicals
- News, earnings, and analyst ratings
- Dividend history
- Options positions (read-only)
- Symbol search
- TOTP-based 2FA support
