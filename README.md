# TravOS onboarding

Setup scripts for a new TravOS developer or tester. Each one installs what the
role needs and is safe to run again.

This repo is public so a new machine can fetch a script before it has git or a
GitHub sign-in. Nothing here is secret. The developer script asks for the one
secret it needs while it runs.

## Developer

Windows, in PowerShell:

```powershell
irm https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-developer.ps1 | iex
```

macOS, in Terminal:

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-developer.sh)"
```

It installs git, the GitHub CLI, the .NET 10 SDK, Node 24 with pnpm, Docker
Desktop, the Aspire CLI, dotnet-ef, VS Code, the Azure CLI and Claude Code. A
tool whose command already works is left alone. The script then:

- trusts the HTTPS dev certificate
- turns on long paths on Windows
- signs you in to GitHub
- clones `travos-aus-agent` into `~/src/travos-aus-agent` and runs its
  `bootstrap.sh`
- creates both portals' `config.json`
- asks for the Auth0 Management secret your lead sent you

It finishes with a checklist of anything still missing.

Already set up? Check your machine against the current requirements without
changing anything:

```powershell
$env:TRAVOS_CHECK=1; irm https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-developer.ps1 | iex
```

```sh
TRAVOS_CHECK=1 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-developer.sh)"
```

Set `TRAVOS_PATH` the same way to clone somewhere other than
`~/src/travos-aus-agent`. On Windows keep the path short. Some files in the
workspace are deep enough to hit the 260-character limit.

## Tester

Windows:

```powershell
irm https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-tester.ps1 | iex
```

macOS:

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/TravOS-AUS/travos-aus-onboarding/main/setup-tester.sh)"
```

It installs Chrome and Firefox, plus Edge on a Mac, then prints the links you
need: the QA Team board and the dev and UAT admin portals.

## Changing a script

The version requirements live in these scripts and nowhere else. The workspace
docs link here instead of listing them. When a repo moves to a new .NET, Node or
Aspire version, change it here in the same piece of work. Then run the check
mode on your own machine before opening the pull request.

## Testing

Nothing here runs in CI. A change to what a script installs gets tested by hand
on a clean machine, on each operating system it touches, before it merges.

**A clean machine.**

- Windows: a new Hyper-V Windows 11 VM. Windows Sandbox is lighter, but it may
  not have winget, and it has to be enabled first.
- macOS: a macOS VM (UTM or Tart on Apple Silicon), or a new macOS user account.
  A new account gets a clean home folder, but it shares Homebrew in
  `/opt/homebrew` with the other accounts.

**The run.** Use the one-liners above, pointed at your branch, by replacing
`main` in the URL with the branch name:

1. Check mode. Almost every row shows FIX, the script reaches its last row,
   "Auth0 secret", and nothing in the home folder changes.
2. The full developer script. Approve the admin prompt, sign in to GitHub in the
   browser and paste the Auth0 secret. The end checklist is all ok except
   "Docker running".
3. Open Docker Desktop once, then check mode again. Every row is ok.
4. The full script again. It installs nothing and asks for nothing.
5. `aspire run` from the clone. The dashboard opens, and the portals answer on
   `http://localhost:4200` and `http://localhost:4300`.
6. The tester script. The browsers install and the links print.

Post the operating system, the CPU architecture and any FIX rows as a comment on
the pull request.

Before any of that, `bash -n setup-developer.sh` and `bash -n setup-tester.sh`
catch a syntax error in seconds. On a Mac, run them with `/bin/bash`, which is
the bash 3.2 a new Mac has.
