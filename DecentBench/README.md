# DecentBench

## Additional requirements

- The benchmark is using YCSB, thus, the additional requirements are the same as the YCSB.
	- Please refer to [YCSB at https://github.com/brianfrankcooper/YCSB](https://github.com/brianfrankcooper/YCSB)
- In general, it needs Java JRE (we're using version 12), and maven
	- make sure their binaries are in the `PATH` enviroment
	- make sure `JAVA_HOME` envrioment variable is set correctly

## How to build

1. Make sure you have run `git submodule update --init --recursive`
2. Run `setup.py` (make sure it's run by Python **3**), which will create a `build` directory and call CMake to setup this directory
3. Go to `build` directory, open the MS Visual Studio solution file.
4. Specifically build target `DhtClientYcsb_Java` first
5. Build entire solution
	1. **Debug** build *does not* check with AuthList, but it prints more information during program execution
	2. **Release** build *does* check with AuthList, but it only prints critical info to the screen
6. If you build from a clean "git clone", it will *fail* at this point, because EDL generated source file is not in source list yet
	1. If so, run `setup.py` again, which will catch the new source file and add then to the source list
	2. And then, build the solution again

## How to run

- You can run DecentHT debugging client or try YCSB benchmark

### DecentHT debugging client

- This is only created for debugging purpose in Visual Studio, thus the program itself doesn't print any status info during execution

1. Make sure Decent Server is running, and its IAS report is printed on the screen
2. Copy `Config.json` from `sources` directory to `build\sources\DhtClientYcsb_App` directory
3. Under `build\sources\DhtClientYcsb_App` directory, run `DhtClientYcsb_Debugger` with the command:

```
.\<Debug or Release>\DhtClientYcsb_Debugger.exe
```

4. The enclave's hash should be printed on the screen
5. Edit `Hash` field in `Config.json` for all services, including the Decent Server and *DecentHT* (you can find Decent Server's hash on its print-out)
	- **NOTE**: In *reality*, clients are expected to verify those hash at their own; however, for *testing purpose*, here we just copy the hash from the print-out
6. Make sure you have the `Config.json` edited correctly, and then *restart* services (including DecentHT), and run client with command:

```
.\<Debug or Release>\DhtClientYcsb_Debugger.exe
```

### YCSB Benchmark

1. Build YCSB bindings with command

```
mvn -pl site.ycsb:decentdht-binding -am clean package
mvn -pl site.ycsb:decentdht-nonenclave-binding -am clean package
```

2.  Once YCSB binding has built successfully, you can use `TesterSuit\YcsbTester_Client.py` and `TesterSuit\YcsbTester_Server.py` to run the test (one on server side, another one on client side)

3. `TesterSuit\YcsbTester_Client.py` and `TesterSuit\YcsbTester_Server.py` only accept configuration files (with `--config` option)
	- We provide example configuration files in `TesterSuit\exp_<side>_config.ycsb.test.json`
	- You may fill in the missing part (already annoted), and adjust other parameters to create your own configurations based on your testing environment
	- You also need to fill in `Config.json` with the correct enclave hash for both DecentHT server and client (same as the one used in DecentHT debugging client)
	- For accuracy, both python scripts need to be run with administrator privilige in order to set CPU affinity and priority
	- For accuracy, make sure you have built solution in Release mode
	- Ensure all corresponding binaries, enclave binaries, `Config.json` are copied to the working directory

### If you want to try "SGX-Only" version

- You need to make the following changes manually:
	- Uncomment `#define DECENT_DHT_NAIVE_RA_VER` in 
		- `sources/Common_Enclave/DhtClient/ConnectionManager.h`
		- `sources/DhtClientYcsb_App/DhtClientApp.cpp`
	
	- Add `#define SIMULATING_ENCLAVE` to the top of the
		- `libs/decent-ra-api/sources/DecentApi/Common/SGX/RaProcessorSp.cpp`
		- `libs/decent-ra-api/sources/DecentApi/Common/SGX/IasConnector.cpp`
	
	- Re-run `setup.py`, and then re-build solution
	
	- Build and run `IasSim` (IAS Simulator), which is available in the Decent Server repo
