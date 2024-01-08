# Flight Delay Reporter
This data analysis project focuses on evaluating domestic flight performance for the month of July 2023 using the dataset in "July23_Flight_Delays.csv." The analysis involves building Python dictionaries to aggregate arrival delays, airlines operating at each airport, and direct connections to those airports. The primary objective is to compare two airports based on key metrics, including the number of direct flights, average flight delay, and the percentage of flights exceeding 14 minutes. The findings aim to provide insights into the operational efficiency and punctuality of the selected airports during the specified period.


```python
def main():
    # Open the CSV file containing flight delay data for July 23
    fh = open("July23_Flight_Delays.csv")
    
    # Build dictionaries from the file data
    airline_dict, delays_dict, airports_dict = build_dictionaries(fh)
    
    # Get user input for two airports
    airport1 = input("Name the first airport (Ex. ATL):")
    airport2 = input("Name the second airport (Ex. CID):")
    
    # Display statistics and write them to a file
    stats(airline_dict, delays_dict, airports_dict, airport1, airport2)
    
    # Close the file
    fh.close()

def build_dictionaries(fh1):
    # Initialize dictionaries to store airline, delay, and airport information
    airline_dict = dict()
    delays_dict = dict()
    airports_dict = dict()

    # Iterate through each line in the file
    for line in fh1:
        data = line.split(",")

        # Skip the header line
        if line.startswith("Airline"):
            continue

        # Populate dictionaries with data
        if data[2] not in airline_dict:
            airline_dict[data[2]] = [data[0]]
            delays_dict[data[2]] = [int(data[3].strip())]
            airports_dict[data[2]] = [data[1]]
        else:
            if data[0] not in airline_dict[data[2]]:
                airline_dict[data[2]].append(data[0])
            delays_dict[data[2]].append(int(data[3].strip()))
            if data[1] not in airports_dict[data[2]]:
                airports_dict[data[2]].append(data[1])

    # Return the populated dictionaries
    return airline_dict, delays_dict, airports_dict

def stats(airline_dict, delays_dict, airports_dict, airport1, airport2):
    # Create a file name based on the selected airports
    file_name = airport1 + "_vs_" + airport2 + ".txt"
    
    # Open a file to write statistics
    fh2 = open(file_name, "w")

    # Write information about airlines and airports for each selected airport
    fh2.write("These airlines fly into %s: %s" % (airport1, str(airline_dict[airport1])))
    fh2.write("\nThese airlines fly into %s: %s" % (airport2, str(airline_dict[airport2])))
    fh2.write("\nThese airports have direct flights into %s: %s" % (airport1, str(airports_dict[airport1])))
    fh2.write("\nThese airports have direct flights into %s: %s" % (airport2, str(airports_dict[airport2])))

    # Calculate and write the number of airlines and airports for each selected airport
    num_airlines1 = len(airline_dict[airport1])
    num_airlines2 = len(airline_dict[airport2])
    num_airports1 = len(airports_dict[airport1])
    num_airports2 = len(airports_dict[airport2])

    # Compare and write the difference in the number of airlines and airports
    if len(airline_dict[airport1]) > len(airline_dict[airport2]):
        fh2.write("\n%s has %d more airlines that fly there than %s. %d vs. %d" % (airport1, num_airlines1 - num_airlines2, airport2, num_airlines1, num_airlines2))
    else:
        fh2.write("\n%s has %d more airlines that fly there than %s. %d vs. %d" % (airport2, num_airlines2 - num_airlines1, airport1, num_airlines2, num_airlines1))

    if len(airports_dict[airport1]) > len(airports_dict[airport2]):
        fh2.write("\n%s has %d more airports that connect than %s. %d vs. %d" % (airport1, num_airports1 - num_airports2, airport2, num_airports1, num_airports2))
    else:
        fh2.write("\n%s has %d more airlines that connect than %s. %d vs. %d" % (airport2, num_airports2 - num_airports1, airport1, num_airports2, num_airports1))

    # Calculate and write the average delay for each selected airport
    avg_delay1 = sum(delays_dict[airport1]) / len(delays_dict[airport1])
    avg_delay2 = sum(delays_dict[airport2]) / len(delays_dict[airport2])

    fh2.write("\nAverage delay for %s: %.0f minutes" % (airport1, avg_delay1))
    fh2.write("\nAverage delay for %s: %.0f minutes" % (airport2, avg_delay2))

    # Calculate and write the percentage of flights that were late for each selected airport
    late1 = sum(1 for delay in delays_dict[airport1] if delay > 14)
    late2 = sum(1 for delay in delays_dict[airport2] if delay > 14)

    late_percent1 = late1 / len(delays_dict[airport1]) * 100
    late_percent2 = late2 / len(delays_dict[airport2]) * 100

    fh2.write("\nFor %s in July, %.1f %% of flights were late" % (airport1, late_percent1))
    fh2.write("\nFor %s in July, %.1f %% of flights were late" % (airport2, late_percent2))

    # Close the file
    fh2.close()

# Call the main function to run the program
main()
```
