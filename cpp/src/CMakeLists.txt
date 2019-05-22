set(MEGASEARCH_SRCS
        main.cpp
        )

add_executable(megasearch ${MEGASEARCH_SRCS})

target_link_libraries(megasearch gtest)

