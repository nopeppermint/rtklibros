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
add_library(${PROJECT_NAME}_convkml src/convkml.c)
add_library(${PROJECT_NAME}_solution src/solution.c)
add_library(${PROJECT_NAME}_geoid src/geoid.c)
add_library(${PROJECT_NAME}_rtkcmn src/rtkcmn.c)
add_library(${PROJECT_NAME}_preceph src/preceph.c)

add_executable(${PROJECT_NAME}_pos2kml app/pos2kml/pos2kml.c)
target_link_libraries(${PROJECT_NAME}_pos2kml
    ${PROJECT_NAME}_convkml
    ${PROJECT_NAME}_solution
    ${PROJECT_NAME}_geoid
    ${PROJECT_NAME}_rtkcmn
    ${PROJECT_NAME}_solution
    ${PROJECT_NAME}_preceph)
set_target_properties(${PROJECT_NAME}_pos2kml
    PROPERTIES OUTPUT_NAME pos2kml
    PREFIX "")

#str2str
add_library(${PROJECT_NAME}_ss2 src/rcv/ss2.c)
add_library(${PROJECT_NAME}_crescent src/rcv/crescent.c)
add_library(${PROJECT_NAME}_skytraq src/rcv/skytraq.c)
add_library(${PROJECT_NAME}_gw10 src/rcv/gw10.c)
add_library(${PROJECT_NAME}_javad src/rcv/javad.c)
add_library(${PROJECT_NAME}_stream src/stream.c)
add_library(${PROJECT_NAME}_sbas src/sbas.c)
add_library(${PROJECT_NAME}_nvs src/rcv/nvs.c)
add_library(${PROJECT_NAME}_ublox src/rcv/ublox.c src/rcvraw.c)
target_link_libraries(${PROJECT_NAME}_ublox
    ${PROJECT_NAME}_ss2
    ${PROJECT_NAME}_crescent
    ${PROJECT_NAME}_gw10
    ${PROJECT_NAME}_javad
    ${PROJECT_NAME}_skytraq
    ${PROJECT_NAME}_crescent
    ${PROJECT_NAME}_sbas
    ${PROJECT_NAME}_novatel
    ${PROJECT_NAME}_rtkcmn
    ${PROJECT_NAME}_rinex
    ${PROJECT_NAME}_rtcm
    ${PROJECT_NAME}_nvs)
add_library(${PROJECT_NAME}_novatel src/rcv/novatel.c)
add_library(${PROJECT_NAME}_streamsvr src/streamsvr.c)

add_executable(${PROJECT_NAME}_str2str app/str2str/str2str.c)
target_link_libraries(${PROJECT_NAME}_str2str
    ${PROJECT_NAME}_stream 
    ${PROJECT_NAME}_rtkcmn 
    ${PROJECT_NAME}_solution 
    ${PROJECT_NAME}_geoid 
    ${PROJECT_NAME}_ublox 
    ${PROJECT_NAME}_skytraq 
    ${PROJECT_NAME}_streamsvr 
    pthread)
set_target_properties(${PROJECT_NAME}_str2str
    PROPERTIES OUTPUT_NAME str2str
    PREFIX "")

#rnx2rtkp
add_library(${PROJECT_NAME}_rinex  src/rinex.c)
add_library(${PROJECT_NAME}_rtkpos  src/rtkpos.c)
target_link_libraries(${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_rtkpos_gsi)
add_library(${PROJECT_NAME}_ephemeris  src/ephemeris.c)
add_library(${PROJECT_NAME}_stec  src/stec.c)
add_library(${PROJECT_NAME}_rtkpos_gsi src/rtkpos_gsi.c)
target_link_libraries(${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_lambda ${PROJECT_NAME}_ppp ${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ephemeris)
add_library(${PROJECT_NAME}_postpos  src/postpos.c)
target_link_libraries(${PROJECT_NAME}_postpos ${PROJECT_NAME}_sbas ${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_rinex)



add_library(${PROJECT_NAME}_lambda  src/lambda.c)
add_library(${PROJECT_NAME}_ionex src/ionex.c)
add_library(${PROJECT_NAME}_ppp_ar src/ppp_ar.c)
add_library(${PROJECT_NAME}_pntpos  src/pntpos.c)
target_link_libraries(${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ionex)
add_library(${PROJECT_NAME}_options src/options.c)
add_library(${PROJECT_NAME}_ppp  src/ppp.c)
target_link_libraries(${PROJECT_NAME}_ppp ${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ppp_ar ${PROJECT_NAME}_stec)

add_library(${PROJECT_NAME}_rtcm3  src/rtcm3.c)
add_library(${PROJECT_NAME}_rtcm3e  src/rtcm3e.c)
add_library(${PROJECT_NAME}_rtcm2  src/rtcm2.c)
add_library(${PROJECT_NAME}_rtcm  src/rtcm.c)
target_link_libraries(${PROJECT_NAME}_rtcm ${PROJECT_NAME}_rtcm3 ${PROJECT_NAME}_rtcm3e ${PROJECT_NAME}_rtcm2)

add_executable(${PROJECT_NAME}_rnx2rtkp app/rnx2rtkp/rnx2rtkp.c)
target_link_libraries(${PROJECT_NAME}_rnx2rtkp ${PROJECT_NAME}_rtkcmn ${PROJECT_NAME}_rinex ${PROJECT_NAME}_rtkpos  ${PROJECT_NAME}_postpos ${PROJECT_NAME}_solution
${PROJECT_NAME}_lambda ${PROJECT_NAME}_geoid ${PROJECT_NAME}_sbas ${PROJECT_NAME}_preceph ${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ephemeris ${PROJECT_NAME}_options ${PROJECT_NAME}_ppp ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_ionex ${PROJECT_NAME}_stec)
set_target_properties(${PROJECT_NAME}_rnx2rtkp
    PROPERTIES OUTPUT_NAME rnx2rtkp
    PREFIX "")


#convbin
add_library(${PROJECT_NAME}_convrnx src/convrnx.c)
target_link_libraries(${PROJECT_NAME}_convrnx ${PROJECT_NAME}_rtkcmn ${PROJECT_NAME}_sbas ${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_rinex)

add_executable(${PROJECT_NAME}_convbin app/convbin/convbin.c src/rcvraw.c)
target_link_libraries(${PROJECT_NAME}_convbin ${PROJECT_NAME}_ublox ${PROJECT_NAME}_rtkcmn ${PROJECT_NAME}_rinex ${PROJECT_NAME}_sbas ${PROJECT_NAME}_convrnx ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_skytraq 
${PROJECT_NAME}_rtkcmn ${PROJECT_NAME}_rtksvr ${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_geoid ${PROJECT_NAME}_solution ${PROJECT_NAME}_lambda
${PROJECT_NAME}_sbas ${PROJECT_NAME}_stream ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_preceph ${PROJECT_NAME}_options ${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ppp ${PROJECT_NAME}_ublox
 ${PROJECT_NAME}_ephemeris ${PROJECT_NAME}_rinex ${PROJECT_NAME}_ionex ${PROJECT_NAME}_novatel)
set_target_properties(${PROJECT_NAME}_convbin
    PROPERTIES OUTPUT_NAME convbin
    PREFIX "")
	
#rtkrcv
add_library(${PROJECT_NAME}_rtksvr src/rtksvr.cc)
add_executable(${PROJECT_NAME}_rtkrcv app/rtkrcv/rtkrcvros.cc)
add_dependencies(${PROJECT_NAME}_rtkrcv ${PROJECT_NAME}_generate_messages_cpp)
target_link_libraries(${PROJECT_NAME}_rtkrcv ${PROJECT_NAME}_rtkcmn ${PROJECT_NAME}_rtksvr ${PROJECT_NAME}_rtkpos ${PROJECT_NAME}_geoid ${PROJECT_NAME}_solution ${PROJECT_NAME}_lambda
${PROJECT_NAME}_sbas ${PROJECT_NAME}_stream ${PROJECT_NAME}_rtcm ${PROJECT_NAME}_preceph ${PROJECT_NAME}_options ${PROJECT_NAME}_pntpos ${PROJECT_NAME}_ppp ${PROJECT_NAME}_ublox 
 ${PROJECT_NAME}_ephemeris ${PROJECT_NAME}_rinex ${PROJECT_NAME}_ionex ${PROJECT_NAME}_nvs ${catkin_LIBRARIES})
set_target_properties(${PROJECT_NAME}_rtkrcv
    PROPERTIES OUTPUT_NAME rtkrcv
    PREFIX "")





