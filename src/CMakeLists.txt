# Python bindings for Packaide

# Find packages for Python library and boost-python.
#
# NOTE: Depending on OS, packaging, etc, these libraries might have
# different names... e.g., boost-python might be called boost-python3
# and PythonLibs might default to Python 2 instead of Python 3. These
# will, for now, require system specific fiddling to fix the versions
# to match whatever works for you locally.
find_package(Python3 REQUIRED COMPONENTS Interpreter Development)
set(BOOST_PYTHON_COMPONENT "python${Python3_VERSION_MAJOR}${Python3_VERSION_MINOR}")
find_package(Boost REQUIRED COMPONENTS ${BOOST_PYTHON_COMPONENT})

# Use correct extension on Apple
if(APPLE)
  set(CMAKE_SHARED_LIBRARY_SUFFIX ".so")
endif(APPLE)

# Configure the target for the bindings library
add_library(PackaideBindings SHARED bindings.cpp)
target_include_directories(PackaideBindings PRIVATE ${PROJECT_SOURCE_DIR}/include ${Python3_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS})
target_link_libraries(PackaideBindings Python3::Python Boost::${BOOST_PYTHON_COMPONENT} PackaideLib)
target_compile_options(PackaideBindings PRIVATE -Wfatal-errors)

# Configure installation. We opt to install into the Python packages
# directory rather than the system library directory, for consistency
# with the Python part of the library
set_target_properties(PackaideBindings PROPERTIES PREFIX "")
install(TARGETS PackaideBindings DESTINATION ".")
