############################################################################
#    Copyright (C) 2014 by Ahmed Charles - acharles@outlook.com            #
#    Copyright (C) 2015-2016 by Stephen Lyons - slysven@virginmedia.com    #
#                                                                          #
#    This program is free software; you can redistribute it and/or modify  #
#    it under the terms of the GNU General Public License as published by  #
#    the Free Software Foundation; either version 2 of the License, or     #
#    (at your option) any later version.                                   #
#                                                                          #
#    This program is distributed in the hope that it will be useful,       #
#    but WITHOUT ANY WARRANTY; without even the implied warranty of        #
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the         #
#    GNU General Public License for more details.                          #
#                                                                          #
#    You should have received a copy of the GNU General Public License     #
#    along with this program; if not, write to the                         #
#    Free Software Foundation, Inc.,                                       #
#    59 Temple Place - Suite 330, Boston, MA  02111-1307, USA.             #
############################################################################

# Should be called before PROJECT.
CMAKE_MINIMUM_REQUIRED(VERSION 3.1)

PROJECT(mudlet)

IF(CMAKE_CONFIGURATION_TYPES)
  ADD_CUSTOM_TARGET(check COMMAND ${CMAKE_CTEST_COMMAND}
                    --force-new-ctest-process --output-on-failure
                    --build-config "$<CONFIGURATION>")
ELSE()
  ADD_CUSTOM_TARGET(check COMMAND ${CMAKE_CTEST_COMMAND}
                    --force-new-ctest-process --output-on-failure)
ENDIF()

ENABLE_TESTING()

IF(POLICY CMP0020)
  CMAKE_POLICY(SET CMP0020 NEW)
ENDIF()

IF(WIN32)
  SET(APP_TARGET mudlet.exe)
ELSEIF(APPLE)
  SET(APP_TARGET Mudlet)
ELSE()
  SET(APP_TARGET mudlet)
ENDIF()

# APP_BUILD should only be empty/null in official "release" builds,
# developers may like to set the MUDLET_VERSION_BUILD environment variable to
# their user and branch names to make it easier to tell different builds apart!
#
# Changing this pair of values affects: ctelnet.cpp, main.cpp, mudlet.cpp
# dlgAboutDialog.cpp and TLuaInterpreter.cpp.  It will not necessarily cause
# those files to be automatically rebuilt so you may need to 'touch' those files if
# the variables are changed and you are not doing a full, clean, rebuild!
# Use APP_VERSION, APP_BUILD and APP_TARGET defines in the source code if needed.
# IMPORTANT:
# To insure consistency please ensure the SAME of the first two values are also
# assigned to the "VERSION" and "BUILD" variables in the native qmake project file,
# i.e. ./src/src.pro
SET(APP_VERSION 3.1.0)
IF(DEFINED ENV{MUDLET_VERSION_BUILD} AND NOT $ENV{MUDLET_VERSION_BUILD} STREQUAL "")
  SET(APP_BUILD $ENV{MUDLET_VERSION_BUILD})
else()
  SET(APP_BUILD "-dev")
ENDIF()

SET(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

ADD_SUBDIRECTORY(3rdparty/communi)
ADD_SUBDIRECTORY(3rdparty/lua_yajl)
# Needed (just) on MacOs as an #include in luazip.h:
IF(APPLE)
  ADD_SUBDIRECTORY(3rdparty/luazip)
ENDIF()

ADD_SUBDIRECTORY(src)
