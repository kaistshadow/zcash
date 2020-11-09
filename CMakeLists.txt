
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
COMMAND sh -x ../libtool --silent --tag=CXX --mode=link /home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/share/../native/bin/ccache g++ -m64 -std=c++11 -g -Wformat -Wformat-security -Wstack-protector -fstack-protector-all -fPIC -pipe -O1 -fwrapv -fno-strict-aliasing -Wno-builtin-declaration-mismatch -pthread -Wl,-z,relro -Wl,-z,now -pie -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/share/../lib -Wl,-rpath=/home/mong/CLionProject/blockchain-sim_master/Install/openssl_1.1.0h/lib -Wl,-rpath=/home/mong/CLionProject/blockchain-sim_master/Install/bdb_4.8.30/lib -Wl,-rpath=/home/mong/CLionProject/blockchain-sim_master/Install/lib -o libzcashd.so zcashd-bitcoind.o -L/home/mong/CLionProject/blockchain-sim_master/Install/openssl_1.1.0h/lib -L/home/mong/CLionProject/blockchain-sim_master/Install/bdb_4.8.30/lib  libbitcoin_server.a libbitcoin_wallet.a libbitcoin_common.a univalue/libunivalue.la libbitcoin_util.a libbitcoin_zmq.a crypto/libbitcoin_crypto.a libzcash.a ../target/x86_64-unknown-linux-gnu/release/librustzcash.a ./leveldb/libleveldb.a ./leveldb/libmemenv.a secp256k1/libsecp256k1.la -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/share/../lib -lboost_system -lboost_filesystem -lboost_program_options -lboost_thread -lboost_chrono -ldb_cxx-6.2 -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/lib -lssl -lcrypto -ldl -pthread -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/lib -lcrypto -ldl -pthread -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/lib -levent_pthreads -levent -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/lib -levent -L/home/mong/CLionProject/blockchain-sim_zcash/external/zcash/zcash/depends/x86_64-unknown-linux-gnu/lib -lzmq -lstdc++ -lpthread -lrt crypto/libbitcoin_crypto.a -lboost_system -lcrypto -lsodium -ldl -lanl
COMMAND ${CMAKE_COMMAND} -E copy libzcashd.so ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so
WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src
)

add_custom_command(OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/src/zcashd
DEPENDS bdb_4.8.30
DEPENDS ${ZCASH_SOURCE}
DEPENDS SHADOW_INTERFACE
COMMAND ./autogen.sh
COMMAND ./zcutil/fetch-params.sh
COMMAND ./zcutil/build.sh
COMMAND make SHELL='sh -x'
WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
)

# Define targets that can be used in same build-tree
add_library(zcash_dev_::Plugin SHARED IMPORTED GLOBAL)
add_dependencies(zcash_dev_::Plugin zcash_dev)
set_target_properties(zcash_dev_::Plugin PROPERTIES
IMPORTED_LINK_INTERFACE_LANGUAGES "C"
IMPORTED_LOCATION ${CMAKE_INSTALL_PREFIX}/plugins/libzcashd.so)



