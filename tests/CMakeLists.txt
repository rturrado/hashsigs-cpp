##############
# Dependencies
##############

# Add GTest
find_package(GTest REQUIRED)

# Add nlohmann/json
FetchContent_Declare(json
    URL https://github.com/nlohmann/json/releases/download/v3.11.3/json.tar.xz
    DOWNLOAD_EXTRACT_TIMESTAMP TRUE
)
FetchContent_MakeAvailable(json)

#################
# Test executable
#################
add_executable(hashsigs_tests
    wotsplus_test.cpp
)

target_link_libraries(hashsigs_tests
    PRIVATE
    hashsigs
    GTest::GTest
    GTest::Main
    nlohmann_json::nlohmann_json
)

# Add tests
add_test(NAME hashsigs_tests COMMAND hashsigs_tests)

# Set test properties
set_tests_properties(hashsigs_tests PROPERTIES
    ENVIRONMENT "GTEST_COLOR=1"
)
