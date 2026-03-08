# Reconnaissance Tool v1.0
This is a tool which has been made to see the open ports on an IPv4 address or a domain name. It is helpful in performing audits of a network by scanning some of the known ports stored in a _.csv_ file.

> [!WARNING]
> This tool is for <ins>_educational_</ins> and <ins>_authorized security auditing_</ins> purposes only. Unauthorized scanning of networks or hosts is illegal and unethical. I assume no liability for misuse of this tool or any damage caused by its execution. <ins>**Always obtain explicit permission before scanning any target**</ins>.

## Features
+ The file argument accepts another file if included in the command. By default, it sticks to '_services.csv_'.
+ The target can be a domain name like <ins>google.com</ins> or an IPv4 address like <ins>127.0.0.1</ins>.
+ It returns a table with the port number, service, severity and description of the same.
+ Based on the severity of the open port, it is shown in bold red, red, yellow or green in decreasing order of severity.

## Installation
There is not much to do in the installation point of view, except:
```pip install -r requirements.txt```

## Usage
I've made sure the tool is easy to use. The command takes three arguments:
+ -f or --file which takes a _.csv_ file for input. It defaults to _services.csv_.
+ -t or --target which takes either an IPv4 address or a domain name
+ -h or --help which tells you the same thing I typed here.
e.g.
```
python project.py --t 127.0.0.1
python project.py --help
```

## Design Choices
1. Concurrency and Performance:
   + I used ```concurrent.futures.ThreadPoolExecutor``` for the scanning engine since scanning a target is delayed mostly by waiting for server responses. This allows probing multiple ports parallely.
   + ```itertools.repeat``` and ```zip``` were used to map the target IP across the list of ports, thereby ensuring there's no repetitive code without using a loop for the same and using memory efficiently.
   + This makes the scan quicker based on the number of threads without sacrificing data integrity.
2. Data-driven Architecture:
   + Users can update the targeted ports, service names, and descriptions by simply modifying or using another _.csv_ file, without having to alter the source code.
   + Each port is assigned a severity label, which makes risk assesment easy during the scan.
3. UI and Experience:
   + Results are displayed in a formatted ```Table``` where the ```Severity``` column is dynamically color-coded.
   + The program also catches ```KeyboardInterrupt```, to ensure a clean exit from the program without returning a raw traceback.
   + Errors in input are also handled in the same manner as above.
4. Robust testing and Verification:
   + The ```scan_port``` function is tested using ```unittest.mock``` to simulate socket behavior, thereby helping to check the results for "Success" and "Connection Refused" states without requiring an active connection.
   + Critical functions like ```validate_target``` and ```scan_ports``` are tested for **hard fail** scenarios using ```pytest.raises(SystemExit)```, ensuring the program terminates safely in case of missing files, arguments or invalid arguments.

## Advisor Contribution
While the core logic, architecture and security focus of this tool were designed and implemented by me, I utilized <ins>Google's Gemini AI</ins> as a high-level strategic advisor throughout the development process.
The AI's specific contributions were:
+ **Library Selection**: Suggesting the transition from ```pyfiglet``` and ```tabulate``` to <ins>Rich</ins> library to create a cohesive and professional CLI and dynamic color-coded triage system.
+ **Concurrency Optimisation**: Advising on the use of ```itertools.repeat``` within ```ThreadPoolExecutor``` to ensure memory-efficient iteration over the target IP/domain and port lists.
+ **Testing Strategy**: Providing the conceptual framework for using ```unittest.mock``` to simulate socket behaviours, allowing the ```scan_port``` function to be tested without having an internet connection.
+ **Code Review**: Acting as a <ins>brutally honest</ins> peer to identify oppurtunity costs, such as removing a dysfunctional progress bar temporarily in favor of a stable, high-performance engine.
All final implementations, debugging of mock assertions, and ethical design choices were my own responsibility as the **Lead Developer**. 

## Testing
To test some of the functions used in this project, simply use:
```pytest test_project.py```

## Future Improvements
+ Add a progress bar.
+ Add ability to save the results in a _.pdf_ file.
+ Make the code compatible with ports being checked in a range.
+ Add an option for a full scan to check for ports from **1** to **65,535**.
+ Rely on an external library or API to check the severity of all the ports in the range, instead of a _.csv_ file.