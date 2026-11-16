# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [0.0.1]

### Added

- Initial App-as-Code service definition for the NetBird agent, image `netbirdio/netbird:0.74.2`.
- Remote-access-only posture: DNS takeover, client routes and server routes are disabled by default.
- Setup-key based enrollment via the `NB_SETUP_KEY` secret.
- README with configuration reference and project icon.
