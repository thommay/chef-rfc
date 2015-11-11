---
RFC: unassigned
Author: Thom May <thom@chef.io>
Status: Draft
Type: Standards Track
---

# Knife v3

The purpose of this RFC is to describe the next generation of the knife
CLI tool.

Currently, one must write knife extensions in ruby. Knife is also
relatively slow, due to the vagaries of loading the ruby VM, finding
extensions, and the joys of anti-virus on certain platforms.

The proposed implementation would provide a framework to allow
developers to create knife extensions in multiple languages, and
improve the responsiveness of many knife sub commands, whilst
maintaining backwards compatibility with existing ruby based knife
extensions.

## Motivation

    As an operator,
    I want knife to be fast,
    so that I can focus on meaningful work.

    As an operator,
    I want my existing knife plugins to work unchanged,
    so that I can focus on meaningful work.

    As a developer,
    I want to be able to write knife plugins in the most appropriate
    language,
    so that I can be as efficient as possible.

## Specification

### Sub Commands

The knife command becomes a dispatcher to extensions. It
contains minimal logic, besides being able to fall through to try
less-specific extensions if an extension elects not to respond to the
user's request.

Extensions will be found from the path, and will work similarly to
`git`, ie `knife-role` will be available as `knife role`. Knife will
attempt to use the most specific extension first, falling back to less
specific ones if necessary. To preserve backwards compatibility, and
generally allow ruby plugins, a ruby helper will be the final fall back.

To decline to respond to a request, extensions must exit with a status
of 64, which is `EX_USAGE` in `sysexits.h`.

Extensions should implement `--command-summary` to present help text to
the user via the basic knife command.

### Configuration

To enable extensions to be written in a language agnostic fashion, the
current ruby specific config will need to be parsed and rendered
statically. To ensure freshness, checksums of the ruby config will be
maintained as part of the rendered config. The location of the most
relevant configuration file will be passed to the subcommand via the
`KNIFE_CONFIG_FILE` environment variable; subcommands are expected to
load and parse the json config themselves.

## Copyright

This work is in the public domain. In jurisdictions that do not allow for this,
this work is available under CC0. To the extent possible under law, the person
who associated CC0 with this work has waived all copyright and related or
neighboring rights to this work.
