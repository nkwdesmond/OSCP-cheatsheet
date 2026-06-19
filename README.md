# OSCP Cheatsheet

A local, single-file HTML OSCP cheatsheet with dynamic command generation.

The cheatsheet lets you paste target machines, users, passwords, and hashes, then select active values from sticky dropdowns. Commands are generated dynamically and can be copied line by line.

## Features

- Sticky **Selected Values** section
- Manual filename typing
- Fixed filename dropdown
- Right-edge hover navigation
- Copy buttons per command
- Copy-all button
- Saved local state in the browser

## Editing Commands

Commands are managed in the JavaScript `COMMAND_SECTIONS` array.

To add a new command section, add another object to `COMMAND_SECTIONS`. The right-edge nav and copy buttons are generated automatically.

Example:

```js
{
  id: "nmap",
  title: "Nmap",
  nav: "Nmap",
  commands: [
    {
      template: "sudo nmap -sV -sC -Pn -oA challenge/{last}/{last} {target}"
    },
    {
      prefix: "161 SNMP",
      template: "sudo nmap -sU -oA challenge/{last}/{last}_UDP {target}"
    }
  ]
}
```

## Command Section Format

Each command section supports:

```js
{
  id: "unique-section-id",
  title: "Section Title",
  nav: "Navigation Label",
  commands: [
    {
      prefix: "Optional label",
      className: "kali",
      template: "command using {target} and other variables"
    }
  ]
}
```

### Fields

| Field | Required | Description |
|---|---:|---|
| `id` | Yes | Unique HTML anchor ID for the section. |
| `title` | Yes | Section title shown in the page. |
| `nav` | No | Label shown in the right-edge navigation. Falls back to `title` if omitted. |
| `commands` | Yes | List of command objects. |
| `prefix` | No | Optional label shown before a command, such as `On Kali`, `On target`, or `Linux`. |
| `className` | No | Optional styling class. Current useful values are `kali` and `target`. |
| `template` | Yes | Command template. Variables are wrapped in braces, such as `{target}`. |

## Supported Template Variables

You can use these variables inside command templates:

```text
{target}
{last}
{targetNet}
{allLastOctets}
{allChallengeDirs}
{user}
{password}
{hash}
{domain}
{kaliIp}
{lport}
{service}
{filename}
```

### Variable Reference

| Variable | Meaning |
|---|---|
| `{target}` | Selected target IP address. |
| `{last}` | Last octet of the selected target IP. |
| `{targetNet}` | `/24` network derived from the selected target IP, such as `172.16.51.0/24`. |
| `{allLastOctets}` | Last octets from all pasted targets. |
| `{allChallengeDirs}` | `challenge/<last_octet>` paths for all pasted targets. |
| `{user}` | Selected username. |
| `{password}` | Selected password. |
| `{hash}` | Selected hash. |
| `{domain}` | Domain value from the Variables section. |
| `{kaliIp}` | Kali IP / LHOST / SRC_IP value. |
| `{lport}` | Listener port value. |
| `{service}` | Selected service, converted to lowercase. |
| `{filename}` | Typed or selected filename. |

## Adding a New Command

Add a new command object to an existing section:

```js
{
  prefix: "On Kali",
  className: "kali",
  template: "python3 -m http.server 80"
}
```

Or add a completely new section:

```js
{
  id: "web-enum",
  title: "Web Enumeration",
  nav: "Web enum",
  commands: [
    {
      template: "whatweb http://{target}"
    },
    {
      template: "nikto -h http://{target}"
    }
  ]
}
```

The section will automatically appear in the right-edge navigation, and each command will automatically receive its own copy button.

## Running Locally

Open the HTML file directly in a browser:

```bash
firefox oscpCheatsheet.html
```

Or place it in a tools directory:

```bash
mkdir -p ~/oscp-tools
cp oscpCheatsheet.html ~/oscp-tools/
firefox ~/oscp-tools/oscpCheatsheet.html
```

## Notes

This is intended as a personal OSCP lab workflow aid. Review generated commands before running them, especially when changing target IPs, credentials, hashes, or payloads.
