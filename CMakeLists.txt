
if (NOT CMAKE_INSTALL_PREFIX OR
CMAKE_INSTALL_PREFIX STREQUAL "/usr/local")
set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_SOURCE_DIR}/../../../Install")
endif()


file(GLOB_RECURSE ZCASH_SOURCE ${CMAKE_CURRENT_SOURCE_DIR} *.c *.h *.cpp)

add_custom_target(zcash_dev ALL
DEPENDS ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so
)


add_custom_command(OUTPUT ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so
DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/zcashd
DEPENDS openssl_1.1.0h
COMMAND sh -x ../libtool --silent --tag=CXX --mode=link /${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/share/../native/bin/ccache g++ -m64 -std=c++11 -g -Wformat -Wformat-security -Wstack-protector -fstack-protector-all -fPIC -pipe -O1 -fwrapv -fno-strict-aliasing -Wno-builtin-declaration-mismatch -pthread -fPIC -shared -rdynamic -Wl,-z,relro -Wl,-z,now -pie -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/share/../lib -Wl,-rpath=/${CMAKE_INSTALL_PREFIX}/openssl_1.1.0h/lib -Wl,-rpath=/${CMAKE_INSTALL_PREFIX}/bdb_4.8.30/lib -Wl,-rpath=/${CMAKE_INSTALL_PREFIX}/lib -o libzcashd.so zcashd-bitcoind.o -L/${CMAKE_INSTALL_PREFIX}/openssl_1.1.0h/lib -L/${CMAKE_INSTALL_PREFIX}/bdb_4.8.30/lib  libbitcoin_server.a libbitcoin_common.a univalue/libunivalue.la libbitcoin_util.a crypto/libbitcoin_crypto.a libzcash.a ../target/x86_64-unknown-linux-gnu/release/librustzcash.a ./leveldb/libleveldb.a ./leveldb/libmemenv.a secp256k1/libsecp256k1.la -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/share/../lib -lboost_system -lboost_filesystem -lboost_program_options -lboost_thread -lboost_chrono -ldb_cxx-6.2 -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/lib -lssl -lcrypto -ldl -pthread -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/lib -lcrypto -ldl -pthread -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/lib -levent_pthreads -levent -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/lib -levent -L/${CMAKE_CURRENT_SOURCE_DIR}/depends/x86_64-unknown-linux-gnu/lib -lzmq -lstdc++ -lpthread -lrt crypto/libbitcoin_crypto.a -lboost_system -lcrypto -lsodium -ldl -lanl
COMMAND ${CMAKE_COMMAND} -E copy libzcashd.so ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so
COMMAND ${CMAKE_COMMAND} -E copy libzcashd.so ${CMAKE_INSTALL_PREFIX}/../tests/regtest/shadow-zcash/libzcashd.so
WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src
)

add_custom_command(OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/src/zcashd
DEPENDS bdb_4.8.30
DEPENDS ${ZCASH_SOURCE}
#DEPENDS SHADOW_INTERFACE
COMMAND ./autogen.sh
COMMAND ./zcutil/fetch-params.sh
COMMAND ./zcutil/build.sh
COMMAND make -j 6
WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
)

# Define targets that can be used in same build-tree
add_library(zcash_dev_::Plugin SHARED IMPORTED GLOBAL)
add_dependencies(zcash_dev_::Plugin zcash_dev)
set_target_properties(zcash_dev_::Plugin PROPERTIES
IMPORTED_LINK_INTERFACE_LANGUAGES "C"
IMPORTED_LOCATION ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so)



