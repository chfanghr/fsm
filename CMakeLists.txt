CMAKE_MINIMUM_REQUIRED(VERSION 3.10)
PROJECT(fsm)

SET(CMAKE_CXX_STANDARD 11)

IF (EXISTS ${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
	INCLUDE(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
	CONAN_BASIC_SETUP()
ELSE ()
	MESSAGE(WARNING "The file conanbuildinfo.cmake doesn't exist, you have to run conan install first")
ENDIF ()

INCLUDE_DIRECTORIES(include)

ADD_LIBRARY(fsm include/fsm/fsm.h include/fsm/events.h include/fsm/errors.h include/fsm/any.h include/fsm/locks.h
			src/fsm.cc src/events.cc include/fsm/utils.h src/utils.cc src/locks.cc)

FIND_PACKAGE(Threads REQUIRED)
SET(FSM_LIB ${CMAKE_THREAD_LIBS_INIT} fsm ${CMAKE_THREAD_LIBS_INIT})

IF (BUILD_EXAMPLES)
	ADD_EXECUTABLE(simple examples/simple.cc)
	ADD_EXECUTABLE(alternative examples/alternative.cc)
	ADD_EXECUTABLE(struct examples/struct.cc)
	ADD_EXECUTABLE(graph examples/graph.cc)
	ADD_EXECUTABLE(async examples/async.cc)
	ADD_EXECUTABLE(runtime examples/runtime.cc)

	TARGET_LINK_LIBRARIES(simple ${FSM_LIB})
	TARGET_LINK_LIBRARIES(alternative ${FSM_LIB})
	TARGET_LINK_LIBRARIES(struct ${FSM_LIB})
	TARGET_LINK_LIBRARIES(graph ${FSM_LIB})
	TARGET_LINK_LIBRARIES(async ${FSM_LIB})
	TARGET_LINK_LIBRARIES(runtime ${FSM_LIB})
ENDIF ()

IF (BUILD_TESTS)
	ENABLE_TESTING()

	ADD_EXECUTABLE(fsm_test tests/fsm_tests.cc)
	TARGET_LINK_LIBRARIES(fsm_test ${FSM_LIB} gtest_main gtest)
	ADD_TEST(NAME FSM_TEST COMMAND fsm_test)
ENDIF ()