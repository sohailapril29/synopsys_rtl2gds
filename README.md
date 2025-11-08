# Pre SignOFF RTL2GDS Using SYNOPSYS Tools




## 1. Inputs for Synthesis

```
RTL Design
```

```
Lib (.lib / .db)
```

```
SDC (constraints)
```

---

## 2. Design Types

```
Flat design
```

```
Hierarchical design
```

---

## 3. Tool Invocation Commands

```
which icc2_shell
```

```
which dc_shell
```

```
which vcs
```

```
which verdi
```

---

## 4. Create a env_file inside RTL folder and



paste below commands

```
export VCS_HOME=/path/to/vcs
```

```
export VERDI_HOME=/path/to/verdi
```

```
export NOVAS_HOME=/path/to/novas
```
then save the file and type below command
```
source env_file
```
navagiate to RTL folder and type 
```
vcs design.v testbench.v -lca -kdb -debug_access+all -full64
```

```
./simv
```

```
./simv -verdi
```
type exit
```
cd DC/
```

```
dc_shell
```

```
source -echo -verbose ./rm_setup/dc_setup.tcl
```

```
set RTL_SOURCE_FILES ./../rtl/full_adder.v
```

```
define_design_lib WORK -path ./WORK
```

```
analyze -format verilog ${RTL_SOURCE_FILES}
```

```
open dc_shell using command stat_gui
```

```
elaborate ${DESIGN_NAME}
```

```
stat_gui
```

```
read_sdc ./../CONSTRAINTS/full_adder.sdc
```

```
get_ports
```

```
change_selection [get_ports]
```

```
get_lib
```

```
compile
```
can you compile or compile_ultra
```
compile_ultra
```

```
report_cells
```

```
report_timing
```

```
write_sdc ./results/full_adder.sdc
```

---

## 5. Reopen DC Session

type exit then navagiate to DC folder
```
dc_shell
```

```
set link_library {* ../ref/lib/stdcell_rvt/saed32rvt_tt0p78vnhoc.db}
```

```
set target_library {../ref/lib/stdcell_rvt/saed32rvt_tt0p78vnhoc.db}
```

```
read_verilog ../rtl/full_adder.v
```

```
start_gui
```

```
read_sdc ../CONSTRAINTS/full_adder.sdc
```

```
source ./run_dc.tcl
```

---

## 6. Floorplanning
- type exit in terminal 
- navagiate to ICC2 folder 
```
icc2_shell
```

```
set PDK_PATH ./../ref
```

```
create_lib -ref_lib $PDK_PATH/lib/ndm/saed32rvt_c.ndm FULL_ADDER_LIB
```

```
read_verilog {./../DC/results/full_adder.mapped.v} -library FULL_ADDER_LIB -design full_adder -top full_adder
```

```
stat_gui
```

```
initialize_floorplan -core_offset 2.5
```

```
save_lib
```

```
save_block
```
- if you want to exit or closed or want to reopen the tool frist type below commands 
```
open_lib FULL_ADDER_LIB1
```

```
open_block full_adder
```

```
start_gui
```

---

## 7. ICC2 Scenario 2

```
initialize_floorplan -core_offset 2
```

```
set_individual_pin_constraints -ports [get_ports] -sides 2
```

```
place_pins -self
```

```
create_placement -floorplan -effort very_low
```

---

## 8. Power Planning

```
connect_pg_net -all_blocks -automatic
```

```
get_ports
```

```
create_net -power {VDD}
```

```
create_net -ground {VSS}
```

```
get_ports
```

```
create_pg_ring_pattern core_ring_pattern -horizontal_layer M7 -horizontal_width 0.4 -horizontal_spacing 0.3 -vertical_layer M8 -vertical_width 0.4 -vertical_spacing 0.3
```

```
set_pg_strategy core_power_ring -core -pattern {{name: core_ring_pattern} {nets: {VDD VSS}} {offset: {0.5 0.5}}}
```

```
compile_pg -strategies core_power_ring
```

```
create_pg_mesh_pattern mesh -layers {{
    {vertical_layer: M6} {width: 0.34} {spacing: interleaving} {pitch: 5} {offset: 0.5}
    {horizontal_layer: M7} {width: 0.38} {spacing: interleaving} {pitch: 5} {offset: 0.5}
}}
```

```
set_pg_strategy core_mesh -pattern {{pattern: mesh} {nets: VDD VSS}} -core -extension {stop: innermost_ring}
```

```
compile_pg -strategies core_mesh
```

```
create_pg_std_cell_conn_pattern std_cell_rail -layers {M1} -rail_width 0.09
```

```
set_pg_strategy rail_strat -core -pattern {{name: std_cell_rail} {nets: VDD VSS}}
```

```
compile_pg -strategies rail_strat
```

```
check_pg_drc
```

```
report_utilization
```

```
report_design -all
```

---

## 9. Placement

```
remove_modes -all;
```

```
remove_corners -all;
```

```
remove_scenarios -all;
```

```
set mode1 "func"
```

```
set corner1 "nom"
```

```
set scenario1 "${mode1}::${corner1}"
```

```
create_mode $mode1
```

```
create_corner $corner1
```

```
create_scenario -name func::nom -mode func -corner nom
```

```
report_scenario
```

```
source ./../CONSTRAINTS/full_adder.sdc
```

```
current_mode func
```

```
current_corner nom
```

```
current_scenario func::nom
```

```
set parasitic1 "p1"
```

```
set tluplus_file$p1 "$PDK_PATH/tech/star_rcxt/saed32nm_1p9m_Cmax.tluplus"
```

```
set layer_map_file$p1 "$PDK_PATH/tech/star_rcxt/saed32nm_tf_itf_tluplus.map"
```

```
set parasitic2 "p2"
```

```
set tluplus_file$p2 "$PDK_PATH/tech/star_rcxt/saed32nm_1p9m_Cmin.tluplus"
```

```
set layer_map_file$p2 "$PDK_PATH/tech/star_rcxt/saed32nm_tf_itf_tluplus.map"
```

```
read_parasitic_tech -tlup $tluplus_file$p1 -layermap $layer_map_file$p1 -name p1
```

```
read_parasitic_tech -tlup $tluplus_file$p2 -layermap $layer_map_file$p2 -name p2
```

```
set_parasitic_parameters -late_spec $parasitic1 -early_spec $parasitic2
```

```
set_app_options -name place.coarse.continue_on_missing_scandef -value true
```

```
place_opt
```

---

## 10. Clock Tree Synthesis

```
synthesize_clock_tree
```

```
set_app_options -name cts.optimize.enable_local_skew -value true
```

```
set_app_options -name cts.compile.enable_local_skew -value true
```

```
set_app_options -name cts.compile.enable_global_route -value false
```

```
set_app_options -name clock_opt.flow.enable_ccd -value true
```

```
clock_opt
```

---

## 11. Routing

```
set_app_options -name route.global.timing_driven -value true
```

```
set_app_options -name route.global.crosstalk_driven -value false
```

```
set_app_options -name route.track.timing_driven -value true
```

```
set_app_options -name route.track.crosstalk_driven -value true
```

```
route_global
```

```
route_track
```

```
route_detail
```

```
route_opt
```

```
report_timing
```

```
set fillers_ref "*/*SHFILL128_RVT */*SHFILL64_RVT */*SHFILL3_RVT */*SHFILL2_RVT */*SHFILL1_RVT"
```

```
create_stdcell_fillers -lib_cell $fillers_ref
```

```
write_gds -merge_files {~/RTL2GS/ref/lib/gds/saed32nm_rvt_b.gds} full.gds
```
- or write_gds ./full.gds
```
write_gds ./full.gds
```

```
read_gds ./full.gds
```

```
open_block full_adder
```

```
start_gui
```

---

## 12. PrimeTime (PT)

```
cd PT
```

```
pt_shell
```

```
set link_path "./../ref/lib/stdcell_rvt/saed32rvt_ss0p7vn40c.db"
```

```
read_verilog "./../ICC2/results/full_adder.routed.v"
```

```
link_design
```

```
read_sdc ./../CONSTRAINTS/full_adder.sdc
```

```
read_parasitics "./../ICC2/results/full_adder_func::nom.spef.p1_125.spef"
```

```
update_timing -full
```

```
report_timing
```

```
report_design
```

```
check_timing
```
```
read_sdc ./../CONSTRAINTS/full_adder.sdc 
```
```
update_timing -full
```
``` 
report_timing
```
```
check_timing
```
```
report_global
```

```
report_global
```

```
start_gui
```


🚀 [Live Demo](https://youtu.be/o_03zqJgs8g)


