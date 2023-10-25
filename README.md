fastpkg(1) -- A packageless package manager
=============================================

## SYNOPSIS

`fastpkg [<OPTIONS>] <ACTION> [<SUB_OPTIONS>]`

`fastpkg-template [-h, --help] <FILE_NAME>`

## DESCRIPTION

A complementary packageless package manager, turn any URL-file into a Linux
like package.

Managing software and files from third party sources can be a pain. This is
especially true for Windows users. Fastpkg is a complementary packageless
package manager that can be used to manage software and files from third party
sources.

It differs from a traditional package managers in that packages don't come in a
specific packaged format like .deb, .nupkg or .exe, that are specific to the
package manager. Instead, packages are downloaded directly from a network and
then installed. Unlike traditional package manager, in fastpkg - how packages
are installed is defined in the repos packages.csv file, not in the package
itself. This means that any package can be used on any system that has fastpkg
installed. As long as the target system support that file type. This means you
can host any kind of file, not just software. Like disk ISOs, website
templates, Windows exes, Windows exes that come as zip files or .deb files that
are hosted on GitHub. It can even be used as a wrapper for other package
managers like APT.

This gives the additional bonus of not having to repackage software and host
large amounts of packages on your own. This also enables distribution of
software, that is not open source or that is otherwise not allowed to be
repackaged. Fastpkg basically enables you to distribute any URL-file as a
package without permission and without infringing on trademarks.

Target requirements:

* POSIX compatible OS (e.g. Linux or Cygwin)
* Python 2.7 or 3

Optional requirements:

* axel (available via shell)
* dtrx (available via shell)
* jigdo-lite (part of jigdo-file) (available via shell)

dtrx can also be installed via fastpkg itself. In the official repo, it is
available as 'fastpkg-dtrx'.

Tested on: Debian 10, 11, 12, Windows XP with Cygwin

## OPTIONS

* `-h`, `--help` : show this help message and exit
* `-a`, `--all` : Use instead of '-p'. Selects all packages in the repo(s).
* `-b`, `--blackwhite` : Don't colorize output
* `-c`, `--continuedl` : Continue an aborted jigdo download
* `-f`, `--force` : Re-download package. This can fix broken downloads.
* `-p`, `--package` `<PACKAGE_NAME>[_<VERSION>]` : Name of a package or
  packages. Separate multiple packages by a space and in quotation marks.
  Specify a version with underscore: `<PACKAGE>_<VERSION>`
* `-q`, `--quiet` : Suppress all confirming output, show only errors and
  warnings.
* `-s`, `--skip` : Skip hash verification, only use if you already know that
  all files are correctly downloaded.
* `-r`, `--repo` `<REPO_URL>` : Specify a single repo to target. Only for use
  with 'enlist'
* `-v`, `--verbose` : Get extra information. -v is recommended for users, -vv
  is basic debugging, -vvv is for full debugging

## ACTIONS

* `download` : Download a package or packages, without installing. Uses
  parallel download.
* `enlist` : Append a repo URL to the repo list file /etc/fastpkg/fastpkg.list
  .
* `exportcache` : Export a custom package-cache to be used on an offline host.
  Define packages with -p or -a to narrow down the package-cache.
* `init` : Create basic folder structure.
* `install` : Download and install a package. Expects that you have a
  package-cache. E.g. ran 'update' beforehand or have copied download-cache and
  package-cache from another host.
* `list` : List all packages. Use -v to see if the packages are downloaded
  and/ore installed.
* `show` : Show package details
* `update` : Update cache for every repo found in /etc/fastpkg/fastpkg.list .

## SUB OPTIONS

For action `show`

* `-d`, `--downloadpath` : Only show the Download-Path variable.

For action `install`

* `-i`, `--installpath` `<PATH>` : Override where to install packages with a
  custom installation path.
* `-o`, `--overwrite` : Install files, even if the target folder already
  exists.
* `-r`, `--rename` : Override the automatic naming of files and paths. Choose
  your own name. Only works in conjunction with the --installpath flag.
* `-n`, `--noversionrename` : Override the automatic naming of files and paths.
  Will use the automatic name but without the '_VERSION'

## COMMON USAGE EXAMPLES

Add a repository (this example adds my personal repo)

    $ fastpkg -r https://muspekaren.se/fpkg enlist

Add a repository from a local file

    $ fastpkg -r file:///opt/my-local-repo/packages.csv enlist

Update all repositories

    $ fastpkg update

List all packages

    $ fastpkg -a list

List all packages, more detailed

    $ fastpkg -va list

Show package details

    $ fastpkg -p wordpress show

Only download a package

    $ fastpkg -p wordpress download

Download and install a package

    $ fastpkg -p wordpress install

## REPO MAINTAINANCE

To create your own repo, all you need is an HTTPS web-server and a packages.csv
file. The packages.csv file is a simple text file that contains information
about the software, such as name, version, download URL, hashes and more. To
create a template for the packages.csv file run 'fastpkg-template <NAME>'. You
can open the CSV file with LibreOffice or similar. The packages.csv file can be
hosted locally or on any HTTPS web-server. HTTPS is mandatory for the repo but
not the packages themselves.

To see what a packages.csv file looks like, just open the one used by the
official repo:  
[muspekaren.se/fpkg/packages.csv](https://muspekaren.se/fpkg/packages.csv)

Because the package repo is a simple CSV file you are not allowed to use commas
in variables. This is a limitation but this also makes management very simple.

Supported archive formats: bz2, gz, lzma, tar, tbz, tgz, tlz, xz, zip

The packages.csv file is made of a table with 8 columns and an unlimited number
of rows. Each row represents a package. Each column represents a variable. The
first line is the header and the following lines are the values. There are 8
variables:

* NAME : name of the application
* VERSION : version number
* EXTENSION : file extension
* INSTALLER : how to install the package
* SCRIPT : customize the installation process
* DESCRIPTION : short description of the package
* SHA256 : hash of the package
* SOURCE : URL to the package

Here is a more in depth explanation of the variables:

#### NAME

Globally unique name of the package, you can use capital letter to make it more
readable. When choosing names think long and hard about the name. Try to use
Linux package compatible names. For example the program QTodoTxt is not
available in APT and has a unique name all over the Internet. So it is safe to
use the name 'QTodoTxt' as is. If you use fastpkg as a wrapper for other
package managers like APT, use the naming scheme of the target package manager.
For example for the program 'debcvescan' use the name
'debcvescan'. Do not use name like 'debian-debcvescan' because it will be clear
from the extension .deb, that this is an APT package. If you need different OS
versions of the same package, it is better to create a separate fastpkg repo
for your packages instead of using a name like 'ZDoom-win' or
'ZDoom-Ubuntu'. When installing none-executable files, that don't have a native
fastpkg installer, it is recommended to use prefixes like
'wallpaper-' for .jpg wallpapers or 'wadfile-' for DOOM .wad files. It is
ultimately up to the package maintainer, to choose a meaningful naming scheme
that works for the users. If a package requires multiple files, all you have to
do is to give it the same NAME and the same VERSION. Fastpkg will automatically
install all files with the same NAME and VERSION.

#### VERSION

Version of the package, use dot separated strings. Fastpkg tries to handle all
available versioning schemes to figure out the latest version.
See https://en.wikipedia.org/wiki/Software_versioning . Versions like 2.03 and
2.04 will work just fine. Prioritization is based on separation by dot and the
order from left to right. Using a date is a good method to version packages
that don't have a release. For example, use the file creation date YYMMDD. If a
files does not have a version, like a wallpaper or a file that never changes,
just use the number '1' as version. Other examples are 1.2.3.beta, 1.2.3.alpha,
repack-1, repack-2.1, 3.5.x32, 3.5.x64 . Even only letters can be used as a
version number. To debug if the latest version is detected correctly run:  
`fastpkg -vvvp myapplication show | grep 'latest version'`  
It is ultimately up to the package maintainer, to choose a meaningful
versioning scheme that works for the users.

#### EXTENSION

File-extension of the file to download. E.g. 'zip', 'exe',
'cfg', 'jpg' and so on. It is not allowed to use an empty string. If you have a
package that does not have an extension, use for example 'data' or 'run' as
extension. This will rename the downloaded file to this extension. This string
is also uses to tell the installer how to behave and detect archive or jigdo
files.

If the extension is '.jigdo' fastpkg will use jigdo-lite to download the
image. Usually you want to use the extension '.iso.jigdo' in your repo.
Make sure your URL points to the .jigdo file and not the .iso or .template
file.

#### INSTALLER

A string that defines what installer to use. For example
'winapp' will install the program on all systems as a Windows application. Even
on Linux. In that way you can install Windows applications on Linux for use
with, for example, WINE. What usually happens is that the package will only be
extracted/copied to a specific location. It is then up to the user how to
execute that program. See in the list below 'Supported installer types'. Some
more examples: 'ansible-role', this will fetch a ZIP file from the SOURCE URL
and extract it to /etc/ansible/roles. Nothing more.

#### SCRIPT

specify custom installer arguments to modify the installation. The following
arguments are supported:

* `ARGS: <SILENT INSTALLER ARGS>` : When installing, will execute the SOURCE
  binary with this specified arguments. For example, if you have an .exe file
  that supports silent install, you can specify the arguments here. For
  example, to install Firefox silently on Windows you can use
  `ARGS: -ms -ma`
* `extract` : This will pre-extract archive files. For example, you have an
  .iso file that is packaged as a .zip file. You can specify 'extract' to
  extract the .zip file before the .iso file is installed. This is also useful
  to pre-extract nested archives.
* `background` : Will be installed as a wallpaper. In Posix this will be
  /usr/share/backgrounds and in Windows this will be
  WIN_ROOT/WINDOWS/Web/Wallpaper

#### DESCRIPTION

A short, one sentence, description of the package. Like for the package '
WordPress' this could be 'Content management system written in PHP'.

#### SHA256

The complete 64 characters long SHA 256 hash string of the source file.

#### SOURCE

URL pointing to the file to download. Make sure to avoid redirects and use
persistent direct download URLs. If you have a file that is hosted on for
example SourceForge, use the direct download URL. Use links that point to the
exact version of a package
like https://github.com/MyUser/MyApp/archive/refs/tags/20180505.zip and
not https://github.com/MyUser/MyApp/archive/refs/heads/main.zip . If you have a
file that is constantly changing but has the same URL, the best way is to use
archive.org to create a snapshot of that file. Or host that file on your own
server. The same goes for URLs that are only available for a limited time or
are cookie/token based. If you need proprietary software, that is only
available for a limited time, you have no choice but host them internally, of
course.

Supported installer types:

| Installer tag | Install directory                            | Description                             |
|---------------|----------------------------------------------|-----------------------------------------|
| ansible-coll  | /etc/ansible/collections/ansible_collections | Ansible Galaxy collection               |
| ansible-role  | /etc/ansible/roles                           | Ansible role                            |
| app           | /opt                                         | Generic Posix compatible application    |
| data          | /var/opt/data                                | Generic none executable files           |
| disk          | /var/opt/disk_images                         | *Disk images, like .iso                 |
| firefox-ext   | /var/opt/firefox_addons                      | *Firefox addon installer                |
| java          | /opt                                         | Java applications, like .jar            |
| libvirt       | /var/lib/libvirt/templates                   | *Libvirt templates, like .xml and qcow2 |
| vm            | /var/opt/vm_templates                        | *Other virtual machine templates        |
| web           | /var/opt/www_templates                       | Website templates, like .html and so on |                          
| winapp        | **WIN_ROOT/opt                               | Windows executables, like .exe or .msi  |
| windata       | **WIN_ROOT/data                              | Generic none executable files           |

*These, usually large, static files will be linked from the downloads
directory to the installation directory.  
**WIN_ROOT is the root of the Windows installation, e.g. C:\

## OUTDATED LINKS

By providing a collection of links from the internet, the chance that one of
those links will be outdated very quickly is high. You are welcome to use my
repo https://muspekaren.se/fpkg in your projects, but There is a high change
that links in my repo will stop working. Please contact me if this happens and
I do my best to update them. A simple solution is to just copy my repo and
replace the broken links with the URL of your own internal file-host. In that
way you don't need to rewrite scrips that use the same package name from my
repo on muspekaren.se/fpkg.

Also see my notes on Miscrosoft ISO
files: [Windows ISOs](https://github.com/ServerMonkey/servermonkeys-devtools/windows-isos.md)

Another good reason to host your own repo is speed. Several packages in my repo
are provided by archive.org which have very slow download speeds.

## COPYRIGHT

See license file

## SEE ALSO

axel(1), dtrx(1), jigdo-file(1), jigdo-lite(1)
