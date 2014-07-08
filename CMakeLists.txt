cmake_minimum_required(VERSION 2.8.3)
project(rtklibros)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED roscpp sensor_msgs std_msgs nav_msgs geometry_msgs rtk_msgs angles roslib)

add_definitions("-Wall -ansi -pedantic -I$(SRC) -DTRACE -lpthread -DENAGLO -DENAQZS -DENAGAL -DNFREQ=3")
 
link_libraries(m)

## Generate services in the 'srv' folder
add_service_files(
   FILES
   SingleResponse.srv
)

## Generate added messages and services with any dependencies listed here
generate_messages(
   DEPENDENCIES
)


catkin_package(
  #INCLUDE_DIRS include
  #LIBRARIES UTMConverter
#  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS system_lib
)

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(src ${catkin_INCLUDE_DIRS})
 
#pos2kml
add_library(convkml src/convkml.c)
add_library(solution src/solution.c)
add_library(geoid src/geoid.c)
add_library(rtkcmn src/rtkcmn.c)
add_library(preceph src/preceph.c)

add_executable(pos2kml app/pos2kml/pos2kml.c)
target_link_libraries(pos2kml convkml solution geoid rtkcmn solution preceph)

#str2str
add_library(ss2 src/rcv/ss2.c)
add_library(crescent src/rcv/crescent.c)
add_library(skytraq src/rcv/skytraq.c)
add_library(gw10 src/rcv/gw10.c)
add_library(javad src/rcv/javad.c)
add_library(stream src/stream.c)
add_library(sbas src/sbas.c)
add_library(nvs src/rcv/nvs.c)
add_library(ublox src/rcv/ublox.c src/rcvraw.c)
target_link_libraries(ublox ss2 crescent gw10 javad skytraq crescent sbas novatel rtkcmn rinex rtcm nvs)
add_library(novatel src/rcv/novatel.c)
add_library(streamsvr src/streamsvr.c)

add_executable(str2str app/str2str/str2str.c)
target_link_libraries(str2str stream rtkcmn solution geoid ublox skytraq streamsvr pthread)

#rnx2rtkp
add_library(rinex  src/rinex.c)
add_library(rtkpos  src/rtkpos.c)
target_link_libraries(rtkpos rtkpos_gsi)
add_library(ephemeris  src/ephemeris.c)
add_library(stec  src/stec.c)
add_library(rtkpos_gsi src/rtkpos_gsi.c)
target_link_libraries(rtkpos lambda ppp pntpos ephemeris)
add_library(postpos  src/postpos.c)
target_link_libraries(postpos sbas rtkpos rtcm rinex)



add_library(lambda  src/lambda.c)
add_library(ionex src/ionex.c)
add_library(ppp_ar src/ppp_ar.c)
add_library(pntpos  src/pntpos.c)
target_link_libraries(pntpos ionex)
add_library(options src/options.c)
add_library(ppp  src/ppp.c)
target_link_libraries(ppp pntpos ppp_ar stec)

add_library(rtcm3  src/rtcm3.c)
add_library(rtcm3e  src/rtcm3e.c)
add_library(rtcm2  src/rtcm2.c)
add_library(rtcm  src/rtcm.c)
target_link_libraries(rtcm rtcm3 rtcm3e rtcm2)

add_executable(rnx2rtkp app/rnx2rtkp/rnx2rtkp.c)
target_link_libraries(rnx2rtkp rtkcmn rinex rtkpos  postpos solution
lambda geoid sbas preceph pntpos ephemeris options ppp rtcm ionex stec)

#convbin
add_library(convrnx src/convrnx.c)
target_link_libraries(convrnx rtkcmn sbas rtkpos rtcm rinex)

add_executable(convbin app/convbin/convbin.c src/rcvraw.c)
target_link_libraries(convbin ublox rtkcmn rinex sbas convrnx rtcm skytraq 
rtkcmn rtksvr rtkpos geoid solution lambda
sbas stream rtcm preceph options pntpos ppp ublox
 ephemeris rinex ionex novatel)
	
#rtkrcv
add_library(rtksvr src/rtksvr.cc)
add_executable(rtkrcv app/rtkrcv/rtkrcvros.cc)
add_dependencies(rtkrcv rtklibros_generate_messages_cpp)
target_link_libraries(rtkrcv rtkcmn rtksvr rtkpos geoid solution lambda
sbas stream rtcm preceph options pntpos ppp ublox 
 ephemeris rinex ionex nvs ${catkin_LIBRARIES})





