cmake_minimum_required(VERSION 3.5)

set(haDecode_src 
Caen1190Module.cxx
Caen775Module.cxx
Caen792Module.cxx
CodaDecoder.cxx
F1TDCModule.cxx
Fadc250Module.cxx
FastbusModule.cxx
GenScaler.cxx
Lecroy1875Module.cxx
Lecroy1877Module.cxx
Lecroy1881Module.cxx
Module.cxx
PipeliningModule.cxx
Scaler1151.cxx
Scaler3800.cxx
Scaler3801.cxx
Scaler560.cxx
SimDecoder.cxx
THaCodaData.cxx
THaCodaFile.cxx
THaCrateMap.cxx
THaEpics.cxx
THaEvData.cxx
THaSlotData.cxx
THaUsrstrutils.cxx
VmeModule.cxx
  )

set(headers
  Decoder.h
  PipeliningModule.h
THaUsrstrutils.h THaCrateMap.h
THaCodaData.h THaEpics.h
THaCodaFile.h SimDecoder.h
THaSlotData.h THaEvData.h
CodaDecoder.h Module.h VmeModule.h
FastbusModule.h Lecroy1877Module.h
Lecroy1881Module.h Lecroy1875Module.h
Fadc250Module.h GenScaler.h
Scaler560.h Scaler1151.h
Scaler3800.h Scaler3801.h
F1TDCModule.h Caen1190Module.h
Caen775Module.h Caen792Module.h
THaBenchmark.h
  )

ROOT_GENERATE_DICTIONARY(haDecodeDict ${headers} 
  INCLUDEDIRS include ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/../src 
  LINKDEF include/haDecode_LinkDef.h  
  OPTIONS -p)
set(PCM_FILE  ${CMAKE_CURRENT_BINARY_DIR}/haDecodeDict_rdict.pcm)

add_custom_target(haDecode_ROOTDICTS DEPENDS ${haDecode_src} ${headers} include/haDecode_LinkDef.h haDecodeDict.cxx)


add_library(dc 
  ${haDecode_src}
  haDecodeDict.cxx
  )
#add_library(PODD::Decode ALIAS dc)

#Set target properties
target_include_directories(dc
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
    PRIVATE
        ${CMAKE_CURRENT_BINARY_DIR}
)
#target_compile_features(dc PRIVATE cxx_auto_type)
#target_compile_options( dc PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)

target_link_libraries(dc
  PUBLIC ${ROOT_LIBRARIES} 
  PUBLIC EVIO::EVIO
  #PRIVATE PODD::HallA
)
#include(GNUInstallDirs)
#set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/JSONUtils)

install(TARGETS dc 
  EXPORT podd-targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  )
#set_target_properties(dc PROPERTIES EXPORT_NAME Decode)

#This is required so that the exported target has the name JSONUtils and not jsonutils
#set_target_properties(HallA PROPERTIES EXPORT_NAME halla)

install(FILES ${headers} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/podd)
install(FILES ${PCM_FILE} DESTINATION lib)

#Export the targets to a script
install(EXPORT podd-targets
  FILE
  PODDTargets.cmake
  NAMESPACE
  PODD::
  DESTINATION
    lib/podd
)
