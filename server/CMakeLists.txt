cmake_minimum_required(VERSION 3.13)

########################################################################################################################

etn_target(static ${PROJECT_NAME}-static
    SOURCES
        src/lib/storage.h
        src/lib/storage.cpp
        src/lib/config.h
        src/lib/config.cpp
        src/lib/daemon.h
        src/lib/daemon.cpp
        src/lib/server.h
        src/lib/server.cpp
        src/lib/task.h

        src/lib/jobs/create.h
        src/lib/jobs/create.cpp
        src/lib/jobs/update.h
        src/lib/jobs/update.cpp
        src/lib/jobs/remove.h
        src/lib/jobs/remove.cpp
        src/lib/jobs/list.h
        src/lib/jobs/list.cpp
        src/lib/jobs/read.h
        src/lib/jobs/read.cpp
        src/lib/jobs/resolve.h
        src/lib/jobs/resolve.cpp
    INCLUDE_DIRS
        src
    USES
        ${PROJECT_NAME}-common
        pthread
        fty-asset-libng
    PRIVATE
)

########################################################################################################################

etn_target(exe ${PROJECT_NAME}
    CONFIGS
        conf/agroup.conf.in
        conf/logger.conf.in
    SOURCES
        src/main.cpp
    USES
        ${PROJECT_NAME}-static
        cxxtools
        tntdb
        fty_common_db
)

etn_configure_file(
    conf/agroup.conf.in

    TARGET      ${PROJECT_NAME}
    DESTINATION ${CMAKE_INSTALL_FULL_SYSCONFDIR}/${PROJECT_NAME}
)

etn_configure_file(
    conf/logger.conf.in

    TARGET      ${PROJECT_NAME}
    DESTINATION ${CMAKE_INSTALL_FULL_SYSCONFDIR}/${PROJECT_NAME}
)

etn_configure_file(
    ${PROJECT_NAME}.service.in

    TARGET      ${PROJECT_NAME}
    DESTINATION /usr/lib/systemd/system/
)

########################################################################################################################

if (BUILD_TESTING)
    etn_test(${PROJECT_NAME}-test
        SOURCES
            test/main.cpp
            test/db.cpp
            test/request.cpp
            test/resolve.cpp
            test/test-utils.h
        CONFIGS
            test/conf/agroup.conf
            test/conf/logger.conf

        INCLUDE_DIRS src
        USES
            ${PROJECT_NAME}-static
            fty_common_db
            tntdb
            mysqld
            stdc++fs
            cxxtools
    )
endif()

########################################################################################################################
