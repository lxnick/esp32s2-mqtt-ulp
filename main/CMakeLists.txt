IF (CMAKE_HOST_WIN32)
    set(IDF_TOOL_PATH "../../esp-idf-v4.4")
ELSE()
    set(IDF_TOOL_PATH "$ENV{IDF_PATH}")
ENDIF()

# Set usual component variables
set(COMPONENT_SRCS 
	"ulp_riscv_example_main.c"
	"mqtt_main.c"
    "${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/mqtt_client.c"
	"${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/lib/platform_esp32_idf.c"
	"${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/lib/mqtt_msg.c"
	"${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/lib/mqtt_outbox.c"
	"${IDF_TOOL_PATH}/examples/common_components/protocol_examples_common/connect.c"
	)

set(COMPONENT_ADD_INCLUDEDIRS 
	"${IDF_TOOL_PATH}/examples/common_components/protocol_examples_common/include"
	"${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/include"
	"${IDF_TOOL_PATH}/components/mqtt/esp-mqtt/lib/include"
	"${IDF_TOOL_PATH}/components/tcp_transport/include"
	"${IDF_TOOL_PATH}/components/esp-tls"
	"${IDF_TOOL_PATH}/components/nghttp/port/include"
	"${IDF_TOOL_PATH}/components/ulp/ulp_riscv/include"
	)
set(COMPONENT_REQUIRES soc nvs_flash ulp driver)

register_component()

#
# ULP support additions to component CMakeLists.txt.
#
# 1. The ULP app name must be unique (if multiple components use ULP).
set(ulp_app_name ulp_${COMPONENT_NAME})
#
# 2. Specify all C and Assembly source files.
#    Files should be placed into a separate directory (in this case, ulp/),
#    which should not be added to COMPONENT_SRCS.
set(ulp_riscv_sources "ulp/main.c")

#
# 3. List all the component source files which include automatically
#    generated ULP export file, ${ulp_app_name}.h:
set(ulp_exp_dep_srcs "ulp_riscv_example_main.c")

#
# 4. Call function to build ULP binary and embed in project using the argument
#    values above.
ulp_embed_binary(${ulp_app_name} "${ulp_riscv_sources}" "${ulp_exp_dep_srcs}")
