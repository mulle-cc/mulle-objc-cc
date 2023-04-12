cmake_minimum_required( VERSION 3.0)

project( mulle-objc-cc NONE)

set( PROJECT_VERSION "0.1.1")

# provide mulle-clang for subsequent crafts. Now most ObjC projects don't
# need a `.mulle/env/craft/definition` dictionary to set mulle-clang, and
# we can transparently pick up musl/cosmopolitan versions that because
# COBJC is only defined --ifneeded
#
INSTALL( DIRECTORY definition
         DESTINATION "share/mulle-craft"
         USE_SOURCE_PERMISSIONS
         PATTERN "*._DS_Store" EXCLUDE
)
INSTALL( DIRECTORY definition.windows
         DESTINATION "share/mulle-craft"
         USE_SOURCE_PERMISSIONS
         PATTERN "*._DS_Store" EXCLUDE
)
INSTALL( DIRECTORY definition.mingw
         DESTINATION "share/mulle-craft"
         USE_SOURCE_PERMISSIONS
         PATTERN "*._DS_Store" EXCLUDE
)

######
#
# must be after CPACK_PACKAGE_VERSION
# include( CMakePackage.cmake)
# include( CPack)
