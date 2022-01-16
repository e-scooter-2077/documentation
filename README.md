# E-Scooter 2077 documentation

This repository contains the documentation regarding the developement process of a DigitalTwin-based monitoring and managing system for a fake e-scooter renting company.

You can start [here](https://e-scooter-2077.github.io/documentation/index.html).

#### Automatic deploy status:  
[![Update GitHub Pages](https://github.com/e-scooter-2077/documentation/actions/workflows/gh-pages.yml/badge.svg?branch=master)](https://github.com/e-scooter-2077/documentation/actions/workflows/gh-pages.yml)

## Project Proposal 

### Members
- Samuele Burattini: samuele.burattini@studio.unibo.it
- Luca Deluigi: luca.deluigi3@studio.unibo.it
- Francesco Dente: francesco.dente@studio.unibo.it
- Simone Magnani: simone.magnani4@studio.unibo.it

### Scenario

The group is committed to design a microservice system that collects and merge e-scooter related data to create a digital twin of the physical entity. The collected data will be:

- Battery percentage
- Position and speed in real-time
- Activity status (rented, idle, etc.)
- Lock state
- Availability for renting
- Any other relevant data discovered while developing

The goal is to maximize the coverage of the renting service by analyzing users and scooters behaviour to map the more requested areas and which kind of trips the users do most in different areas.

To also make use of the power of the digital twins onto their corresponding physical device a mechanism of battery conservation is put in place to guarantee a long lasting battery life by limiting speed when the battery is too low and a remote locking and disabling mechanism that activates when the scooter is outside of the recovery service area or when the battery is below the minimum threshold to guarantee the device not to turn off.

Monitoring will be made possible through an admin dashboard that show the overall state of all the devices in real-time.

### Course requirements
The system will be designed with a domain driven approach. 
In detail the team will have:

- an initial knowledge crunching session (with fake domain experts)
- the definition, mantain and use of the ubiquitous language
- a requirement analysis, carried out through user stories and use-case diagrams
- bounded context partitioning and definition of a context-map
- domain models for each relevant bounded context

Apart from that the project will follow a development process based on continuos integration & deployment for most of the system components. The tools used will be:

- Dotnet CLI for build automation
- Github Actions for continuous integration
- Github Pages for the hosting of the automatic generated documentation
- Microsoft Azure as a target platform for the deployment of the whole system
- Quality Assurance tools to be defined during developement (linter, automatic testing etc.)
