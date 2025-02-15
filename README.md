# Lumen's Auto-Rice Bootstrapping Scripts (LARBS)

## Key differences from Luke's version
- listGenerator is the script I use to generate progs.csv.
  + It is not perfect and may sometimes need manual editing when programs must be installed in a specific order.
  + It also does not automatically add programs installed from git repos.
  + AUR packages are supported, but mileage may vary.
  + If you look near the bottom of the file, you will see a comment about pushing progs.csv to a git repo.  I intentionally left this section blank in this version for you to insert your own method, as I'm sure that I manage my dotfiles differently from you.  This script is usable without it, but you will have to put progs.csv somewhere that larbs.sh can access it.
- By default this fork pulls from my private dotfiles, you will have to change the $dotfilesrepo and $progsfile variables at the top of larbs.sh.
- Alternatively, you can simply use the `-r` and `-p` arguments to suit your setup.

 
## Installation:

On an Arch-based distribution as root, run the following:

```
curl -LO https://raw.githubusercontent.com/L1minus1/LARBS/master/larbs.sh
sh larbs.sh
```

That's it.

## What is LARBS?

LARBS is a script that autoinstalls and autoconfigures a fully-functioning
and minimal terminal-and-vim-based Arch Linux environment.

LARBS can be run on a fresh install of Arch or Artix Linux, and provides you
with a fully configured diving-board for work or more customization.

## Customization

- `-r`: custom dotfiles repository (URL)
- `-p`: custom programs list/dependencies (local file or URL)
- `-a`: a custom AUR helper (must be able to install with `-S` unless you
  change the relevant line in the script

### The `progs.csv` list

LARBS will parse the given programs list and install all given programs. Note
that the programs file must be a three column `.csv`.

The first column is a "tag" that determines how the program is installed, ""
(blank) for the main repository, `A` for via the AUR or `G` if the program is a
git repository that is meant to be `make && sudo make install`ed.

The second column is the name of the program in the repository, or the link to
the git repository, and the third column is a description (should be a verb
phrase) that describes the program. During installation, LARBS will print out
this information in a grammatical sentence. It also doubles as documentation
for people who read the CSV and want to install my dotfiles manually.

Depending on your own build, you may want to tactically order the programs in
your programs file. LARBS will install from the top to the bottom.

If you include commas in your program descriptions, be sure to include double
quotes around the whole description to ensure correct parsing.

### The script itself

The script is extensively divided into functions for easier readability and
trouble-shooting. Most everything should be self-explanatory.

The main work is done by the `installationloop` function, which iterates
through the programs file and determines based on the tag of each program,
which commands to run to install it. You can easily add new methods of
installations and tags as well.

Note that programs from the AUR can only be built by a non-root user. What
LARBS does to bypass this by default is to temporarily allow the newly created
user to use `sudo` without a password (so the user won't be prompted for a
password multiple times in installation). This is done ad-hocly, but
effectively with the `newperms` function. At the end of installation,
`newperms` removes those settings, giving the user the ability to run only
several basic sudo commands without a password (`shutdown`, `reboot`,
`pacman -Syu`).
