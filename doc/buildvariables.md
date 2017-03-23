# Build system

\brief Documents variables to control the build system and provided CMake
       modules

## Variables passable as CMake arguments

### Useful variables provided by CMake itself
* `CMAKE_INSTALL_PREFIX=path`: specifies the final install prefix (temporary
  install prefix is set via `make` argument `DESTDIR=path`)
* `CMAKE_BUILD_TYPE=Release/Debug`: specifies whether to do a debug or a release
  build
* `CMAKE_SKIP_BUILD_RPATH=OFF`: ensures the rpath is set in the build tree
* `CMAKE_INSTALL_RPATH=rpath`: sets the rpath used when installing

### Custom variables
The following variables are read by the CMake modules provided by c++utilities
and qtutilities.

None of these are enabled or set by default, unless stated otherwise.

* `LIB_SUFFIX=suffix`: suffix for library install directory
* `LIB_SUFFIX_32=suffix`: suffix for library install directory, used when
  building for 32-bit platforms
* `LIB_SUFFIX_64=suffix`: suffix for library install directory, used when
  building for 64-bit platforms
* `ENABLE_STATIC_LIBS=ON/OFF`: enables building static libs
* `DISABLE_SHARED_LIBS=ON/OFF`: disables building shared libs
* `STATIC_LINKAGE=ON/OFF`: enables linking applications against static libraries
* `STATIC_LIBRARY_LINKAGE=ON/OFF`: enables linking dynamic libraries against
  static libraries
* `SHELL_COMPLETION_ENABLED=ON/OFF`: enables shell completion in general
  (enabled by default)
* `BASH_COMPLETION_ENABLED=ON/OFF`: enables Bash completion (enabled by
  default)
* `LOGGING_ENABLED=ON/OFF`: enables further loggin in some applications
* `FORCE_OLD_ABI=ON/OFF`: forces use of old C++ ABI
  (sets `_GLIBCXX_USE_CXX11_ABI=0`)
* `EXCLUDE_TESTS_FROM_ALL=ON/OFF`: excludes tests from the all target
  (enabled by default)

#### Windows specific
* `USE_NATIVE_FILE_BUFFER=ON/OFF`: use native function to open file streams
  to pass unicode file names correctly, changing this alters ABI
* `FORCE_UTF8_CODEPAGE=ON/OFF`: forces use of UTF-8 codepage in terminal
* `WINDOWS_RESOURCES_ENABLED=ON/OFF`: enables creating resources for
  application meta data and icon (enabled by default)

#### Qt specific
* `WIDGETS_GUI=ON/OFF`: enables Qt Widgets GUI for projects where it is
  available and optional
* `QUICK_GUI=ON/OFF`: enables Qt Quick GUI for projects where it is available
  and optional
* `BUILTIN_TRANSLATIONS=ON/OFF`: enables built-in translations in applications
* `BUILTIN_ICON_THEMES=breeze;breeze-dark;...`: specifies icon themes to
  built-in
* `BUILTIN_ICON_THEMES_IN_LIBRARIES=breeze;breeze-dark;...`: same as above but
  also affects libraries
* `SVG_SUPPORT=ON/OFF`: enables SVG support if not enabled anyways by the
  project
* `SVG_ICON_SUPPORT=ON/OFF`: enables SVG icon support (only affect static
  builds where the required Qt plugin will be built-in if this variable is
  enabled)
* `WEBVIEW_PROVIDER=auto/webkit/webengine/none`: specifies the Qt module to use
  for the web view
* `JS_PROVIDER=auto/script/qml/none`: specifies the Qt module to use
  for the JavaScript engine
* `QT_LINKAGE=AUTO_LINKAGE/STATIC/SHARED`: specifies whether to use static
  or shared version of Qt (only works with Qt packages provided in the AUR)
* `ADDITIONAL_QT_MODULES=Network;Concurrent;...`: specifies additional Qt
  modules to link against (only use for modules which can not be added
  automatically)


## Variables to be set in project file
The following variables are read by the CMake modules provided by c++utilities
and qtutilities.

### Meta data
* `META_PROJECT_NAME=name`: specifies the project name which is used as the
  application/library name, mustn't contain spaces
* `META_APP_NAME=The Name`: specifies a more readible version of the project
  name used for instance in about dialog and desktop file
* `META_APP_AUTHOR`: specifies the author shown in for instance in about
  dialog
* `META_APP_DESCRIPTION`: specifies a description shown for instance in about
  dialog and desktop file
* `META_GENERIC_NAME`: specifies a generic name for the desktop file
* `META_VERSION_MAJOR/MINOR/PATCH=number`: specifies the application/library
  version, default is 0
* `META_PROJECT_TYPE=application/library/plugin/qtplugin`: specifies whether
  to build an application, a library or a plugin
* `META_CXX_STANDARD=11/14/..`: specifies the C++ version, default is 14

### Files
* `HEADER_FILES`/`SRC_FILES`: specifies C++ header/source files
* `TEST_HEADER_FILES`/`TEST_SRC_FILES`: specifies C++ header/source files of the
  tests
* `TS_FILES`: specifies Qt translations
* `RES_FILES`: specifies Qt resource files
* `DBUS_FILES`: specifies files for Qt DBus
* `WIDGETS_HEADER_FILES`/`WIDGETS_SRC_FILES`: specifies C++ header/source files
  only required for Qt Widgets GUI
* `QML_HEADER_FILES`/`QML_SRC_FILES`/`QML_RES_FILES`: specifies C++
  header/source files and Qt resouce files only required for Qt Quick GUI
* `DOC_FILES`: additional markdown files to be inlcuded in the documentation
  created via Doxygen; the first file is used as the main page
* `DOC_ONLY_FILES`: specifies documentation-only files
* `REQUIRED_ICONS`: names of the icons required by the application and the
  used libraries (can be generated with `qtutilities/scripts/required_icons.sh`)
* `CMAKE_MODULE_FILES`/`CMAKE_TEMPLATE_FILES`: specifies CMake modules/templates
  provides by the project; those files are installed so they can be used by
  other projects

### Additional build variables
* `META_PRIVATE/PUBLIC_COMPILE_DEFINITIONS`: specifies private/public compile
  definitions
* `LIBRARIES`: specifies libraries to link against
* `META_PUBLIC_QT_MODULES`: specifies Qt modules used in the public library
  interface

## Provided modules
Most important modules:
* `BaseConfig`: does basic configuration, reads most of the `META`-variables
* `LibraryTarget`: does further configuration for building dynamic and static
  libraries and plugins; `META_PROJECT_TYPE` must be set accordingly
* `AppTarget`: does further configuration for building an application;
  `META_PROJECT_TYPE` must be set accordingly
* `ShellCompletion`: enables shell completion; only works when using the
  argument parser provided by the ApplicationUtilities::ArgumentParser class
  of course
* `TestTarget`: adds the test target `check` which is *not* required by target
  `all`; uses files specified in `TEST_HEADER_FILES`/`TEST_SRC_FILES` variables
* `Doxygen`: adds a target to generate documentation using Doxygen
* `WindowsResources`: handles creation of windows resources to set application
  meta data and icon
* `ConfigHeader`: generates `resources/config.h`, must be included as the last
  module (when all configuration is done)

The inclusion order of the modules matters.
For an example, checkout the project file of c++utilities itself. The project
files of [Syncthing Tray](https://github.com/Martchus/syncthingtray) should
cover everything (library, plugin, application, tests, desktop file, Qt
resources and translations, ...).