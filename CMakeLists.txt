cmake_minimum_required(VERSION 3.4)
  
  set(CMAKE_CXX_STANDARD 11)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  
  #Добавляем опцию BUILD_TESTS. Если она включена, вместе с самой библиотекой собираются тесты 
  option(BUILD_TESTS "Build tests" OFF)
  
  if(BUILD_TESTS)                   # Если надо собрать тесты, добавляем опцию компилятора —coverage,
    add_compile_options(--coverage) # которая оставляет после прогонки кода отчёт о покрытии
  endif()
  
  project (hello)
  
  #Делаем библиотеку banking и добавляем директорию с заголовками
  add_library(hello STATIC ${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp ${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp)
  target_include_directories(hello PUBLIC
  ${CMAKE_CURRENT_SOURCE_DIR}/hello )
  
  #Линкуем к библиотеке gcov - он нужен для отчётов о покрытии
  target_link_libraries(hello gcov)
  
  #Собираем тесты из исходников, лежащих в директории tests
  if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(third-party/gtest)
    file(GLOB HELLO_TEST_SOURCES tests/*.cpp)
    add_executable(check ${HELLO_TEST_SOURCES})
    target_link_libraries(check hello gtest_main)
    add_test(NAME check COMMAND check)
  endif()
