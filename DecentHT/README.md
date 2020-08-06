# DecentHT

## How to build

1. Make sure you have run `git submodule update --init --recursive`
2. Run `setup.py` (make sure it's run by Python **3**), which will create a `build` directory and call CMake to setup this directory
3. Go to `build` directory, open the MS Visual Studio solution file.
4. Build solution
	1. **Debug** build *does not* check with AuthList, but it prints more information during program execution
	2. **Release** build *does* check with AuthList, but it only prints critical info to the screen
5. If you build from a clean "git clone", it will *fail* at this point, because EDL generated source file is not in source list yet
	1. If so, run `setup.py` again, which will catch the new source file and add then to the source list
	2. And then, build the solution again

## How to run

1. Make sure Decent Server is running, and its IAS report is printed on the screen
2. Copy `Config.json` from `sources` directory to `build` directory
3. Under `build` directory, run `DecentDht_App` with the command:

```shell
.\<Debug or Release>\DecentDht_App.exe -t <total_num_of_nodes> \
                                       -i <index_of_this_node>
```

4. The enclave's hash should be printed on the screen
5. Edit `Hash` field in `Config.json` for all services, including the Decent Server (you can find Decent Server's hash on its print-out)
	- **NOTE**: In *reality*, clients are expected to verify those hash at their own; however, for *testing purpose*, here we just copy the hash from the print-out
6. Make sure you have the `Config.json` edited correctly, and then *restart* services with command:

```shell
.\<Debug or Release>\DecentDht_App.exe -t <total_num_of_nodes> \
                                       -i <index_of_this_node> \
                                       -s <port_num_of_this_node> \
                                       -p <optional_port_num_of_a_existing_node(if_this_is_not_the_first_node)>
```

7. A DecentHT client for debugging purpose is available in DecentBench, please refer to that directory
	- It's only created for debugging purpose in Visual Studio, thus the program itself doesn't print anything during execution

### If you want to try "SGX-Only" version

- You need to make the following changes manually:
	- In `CMakeLists.txt`, uncomment line 269 and 270
	```
	target_compile_definitions(DecentDht_Enclave PRIVATE DECENT_DHT_NAIVE_RA_VER)
	target_compile_definitions(DecentDht_App PRIVATE DECENT_DHT_NAIVE_RA_VER)
	```
	
	- Add `#define SIMULATING_ENCLAVE` to the top of the
	`libs/decent-ra-api/sources/DecentApi/Common/SGX/RaProcessorSp.cpp`
	`libs/decent-ra-api/sources/DecentApi/Common/SGX/IasConnector.cpp`
	
	- Re-run `setup.py`, and then re-build solution
	
	- Build and run `IasSim` (IAS Simulator), which is available in the Decent Server repo
