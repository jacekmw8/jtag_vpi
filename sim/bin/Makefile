SOURCE_DIR=../..
BENCH_DIR=$(SOURCE_DIR)/bench
INCLUDE_DIR=$(BENCH_DIR)/include

# Set V=1 when calling make to enable verbose output
# mainly for debugging purposes.
ifeq ($(V), 1)
Q=
QUIET=
else
Q ?=@
QUIET=-quiet
endif

BITS ?= 64
IVERILOG_VPI_SRC = $(SOURCE_DIR)/jtag_common.c $(SOURCE_DIR)/jtag_vpi.c
#IVERILOG_VPI_SRC = $(SOURCE_DIR)/jtag_vpi.c
ADV_DEBUG_SRC = $(shell ls $(BENCH_DIR)/adv_debugsys/*.v)

IVERILOG_SRC = $(SOURCE_DIR)/jtag_vpi.v \
               $(BENCH_DIR)/jtag_tap/jtag_tap.v \
               $(BENCH_DIR)/jtag_vpi_tb.v \
               $(BENCH_DIR)/ram/ram_wb_b3.v \
               $(ADV_DEBUG_SRC)

all: sim_xlm

build_icarus:
	@echo '##### Icarus Building ######'
	$(Q)gcc -otest_client $(BENCH_DIR)/test_client.c
	$(Q)iverilog-vpi --name=jtag_vpi $(IVERILOG_VPI_SRC) > /dev/null
	$(Q)iverilog -I$(INCLUDE_DIR) -ojtag_vpi_tb.vvp $(IVERILOG_SRC)

build_xlm:
	@echo '##### Xcelium Building ######'
	$(Q)gcc -m$(BITS) -otest_client $(BENCH_DIR)/test_client.c
	$(Q)xrun -$(BITS) -elaborate -top jtag_vpi_tb -access rwc -incdir $(INCLUDE_DIR) $(IVERILOG_SRC) -DCDS_VPI $(IVERILOG_VPI_SRC)

sim_icarus: build_icarus
	@echo '##### Icarus Start the simulation ######'
	$(Q)vvp -n -M. -mjtag_vpi jtag_vpi_tb.vvp +jtag_vpi_enable=1 +dump_enable=1 &
	@sleep 1
	@echo '##### Running the test client ######'
	$(Q)./test_client
	@sleep 1

sim_xlm: build_xlm
	@echo '##### Xcelium Start the simulation ######'
	$(Q)xrun -R +jtag_vpi_enable 
	@sleep 5
	@echo '##### Running the test client ######'
	$(Q)./test_client
	@sleep 1

clean:
	@rm -rf *.o *~ *.vpi *.vvp *.vcd test_client *.log *.history *.key xcelium.d .simvision
