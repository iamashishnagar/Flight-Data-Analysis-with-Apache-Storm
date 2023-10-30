# Flight-Data-Analysis-with-Apache-Storm
A distributed system for the analysis of live flight data to optimize air traffic monitoring across U.S. airports.

Practices distributed data streaming and analysis, using Apache Storm. As an example dataset, we will use the Open Air Traffic Data for Research website (at https://opensky-network.org/) and count the number of flights departing from or arriving at each US major airport per airline company.

TopologyMain.java
TopologyMain.java runs a topology in Apache Storm to read flight data from a file, identify hub airports, and sort flights by airline. It uses the `TopologyBuilder` and `Config` classes to define the topology and set configuration options. The program also includes custom bolts `HubIdentifier` and `AirlineSorter`. Console output logs are redirected to a file, and the program waits for 10 seconds before shutting down the topology. Additionally, it computes and prints the total running time.

FlightsDataReader.java (Spout)
FlightsDataReader.java reads flight data from a file and emits it as a tuple using the Storm framework. The `FlightsDataReader` class extends the `BaseRichSpout` class and implements methods for initializing, reading, and emitting data. The program also includes utility methods for printing colored text to the console. The `readFlightData` method reads data from a `BufferedReader` object and skips lines that start with specific characters. The `open` method initializes the `FlightsDataReader` object, and the `declareOutputFields` method declares the output fields.

HubIdentifier.java (Bolt-1)
The “HubIdentifier” class reads airport data from a file and emits a tuple with the airport city, airport code, and flight. The program has a utility method called "colorPrint" that prints messages to the console with colored background and foreground. It also has a nested class called "AirportData" which represents information about an airport, including its city, airport code, latitude, and longitude. The "execute" method receives a tuple, checks whether the flight is close enough to any of the airports, and emits a tuple with the airport city, airport code, and flight if it is. The "declareOutputFields" method declares that the class emits a tuple with three fields: airport.city, airport.code, and flight.

AirlineSorter.java (Bolt-2)
The “AirlineSorter” class reads the data emitted by the HubIdentifier bolt, sorts the data by airport code or call signs, and prints the statistics for each airport to the console. The sorting can be based on either call signs - the number of flights for airlines, and on airport codes for airports. The data is printed to the console in a formatted manner. The class contains several methods, including `colorPrint`, which is a utility function that prints the given message to the console with a colored cyan background and yellow foreground. The class also contains `prepare` and `cleanup` methods that initialize necessary variables and perform the cleanup of maps. The class also has a couple of private methods, including `printAirportInfo`, which prints the information about an airport, including the airport name, number of flights, and statistics for each airline.

Discussions

Execution Performance (outputs)
The program's execution seems efficient for a single node, but setting it up and running it in cluster mode posed challenges. Interestingly, the total running time for single-thread execution was comparable to the initial multi-threaded execution. Our optimization efforts revealed that HubIdentifier (4 threads) and AirlineSorter (2 to 3 threads) were the optimal thread counts. Sorting call signs based on the number of flights incurred additional running time. The software fetched data from a URL and stored it in a map, enabling it to provide airline names linked to airline codes.

Additional Features
I have added two new features and their implementation and output have been documented above. The first one is "Sort by Call Signs," which sorts the flights in descending order along with the airline names. The second feature, "Sort by Airport Codes," arranges the airport codes in alphabetic order and can be sorted with or without the call-sign option. We have also made it possible to execute the system in cluster mode on several computing nodes.

Limitations
The program has a few limitations that should be addressed. The program's exception handling could be improved to cover edge cases and ensure smooth execution. For example, proper exception handling could be added to handle scenarios such as invalid user input, unexpected network issues, or file I/O errors. This would help in providing better error messages to the users and prevent the program from crashing due to unexpected errors. Additionally, the current implementation of cluster mode is limited to a single master node and multiple worker nodes. It would be beneficial to allow a more distributed setup where each node can act as a master node and perform parallel computations.

Possible Improvements
Numerous potential enhancements could be implemented for the program. Firstly, expanding the dataset size could facilitate the incorporation of more intricate features, resulting in a better capacity to apply the findings to new data. Secondly, enabling multi-computing node execution for cluster mode would offer an outstanding improvement opportunity, leveraging the complete peer-to-peer distributed systems. Furthermore, additional features could be added, such as real-time visualization of the flight paths or an alert system that sends notifications when a flight is delayed or canceled. Finally, incorporating machine learning algorithms to identify patterns and trends in the data could provide additional insights into flight data.
