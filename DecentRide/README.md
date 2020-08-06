# DecentRide

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
3. Under `build` directory, run `PassengerMgm`, `TripPlaner`, `TripMatcher`, `Billing`, `Payment`, and `DriverMgm` in any order you like, with the command:
	`.\<Debug or Release>\<ServiceName>_App.exe`
4. Each service should print their enclave's hash on the screen
5. Edit `Hash` field in `Config.json` for all services, including the Decent Server (you can find Decent Server's hash on its print-out)
	- **NOTE**: In *reality*, clients are expected to verify those hash at their own; however, for *testing purpose*, here we just copy the hash from the print-out
6. Make sure you have the `Config.json` edited correctly, and then *restart* all the services
7. Run the "Passenger"-end and "Driver"-end, under `build` directory also, by using the command:
	`.\<Debug or Release>\<Passenger or Driver>.exe`
8. Follow the steps in "Passenger" end and "Driver" end; press enter to proceed to the next step
	- Note: Passenger process will halt to wait for a driver to match, thus, make sure a driver has picked up the trip, otherwise, the Passenger process will just keep waiting
	- When there're multiple Passengers and Drivers, you can check the trip ID to see whose trip is picked up by the driver
