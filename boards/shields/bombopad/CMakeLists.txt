if (CONFIG_ZMK_DISPLAY)
    zephyr_library_sources(
        custom_status_screen.c
        widgets/art.c
        widgets/bolt.c
        widgets/status.c
        widgets/util.c
    )
    zephyr_library_include_directories(widgets)
    zephyr_library_include_directories(${APPLICATION_SOURCE_DIR}/include)
endif()
