# 1. What is PCM??
The Propulsion, Controls and Modelling (PCM) sub-team develops the software that serves as the "brain" of the vehicle's custom propulsion system, replacing and extending the functionality of the stock vehicle controller. This software enables seamless integration between the original GM vehicle systems and team-added components such as electric drive units, power control hardware, and user interfaces. To design and validate these systems, the team creates detailed physical models in MATLAB Simulink, which are used to evaluate architectures, refine control strategies, and optimize performance before implementation. The resulting control software is deployed to a real-time Speedgoat target (This is a low-powered computer running a Real-Time Operating System) and undergoes a rigorous testing process, including model-in-the-loop (MIL) and hardware-in-the-loop (HIL) validation, to ensure reliability and safety. The team also performs tuning and calibration activities to deliver robust, efficient, and responsive vehicle operation.

# 2. Introduction
Hello and welcome to the PCM development challenge. Here you will be tasked with implementing a control system in a group of 1-3 people. You will be provided with a model of a system to control (the plant) and a system specification from which requirements can be derived and implemented within MATLAB Simulink. The goal of this challenge is to give you a taste of what it is like to work on PCM, and to give you an opportunity to learn about the software development process.

This year the challenge will be to implement a control system that will control a 10-speed automatic transmission in a longitudinal vehicle model (This means the vehicle will be modeled in terms of its motion along a single axis, forward and backward). The control system will be responsible for determining when to shift gears based on feedback from the vehicle dynamics, as well as other factors such as engine load and throttle position. The control system will also need to ensure that the vehicle operates within certain performance and efficiency requirements.

In addition to completing the challenge, each team must submit a breakdown of work, outlining who performed which tasks in the form of a Markdown file within the team's git repository. The challenge contains four difficulty levels based on academic year. The required challenge level is determined by the most senior student in the group. Generally, it is preferred that groups are made up of the same year so that all team members can provide an equal contribution as members are admitted on an individual basis, not as a full team. 

<span style="color:red">If after reading this, PCM sounds like something you'd be interested in, go ahead and complete the [development environment setup steps](Guides/DevEnvironmentSetup.md) and come back to this document once complete.</span>

**Note: Student teams consisting of lower levels may complete higher-level challenge tasks as stretch goals if they are interested to increase the depth of their final presentation, however this is not required.**

# 3. Controls 101
For those of you who are not familiar with the field of controls, this section introduces several concepts that will appear throughout the challenge.
## 3.1. What is a Controller?
A controller is a system that manages, commands, directs, or regulates the behavior of other devices or systems. 
In this challenge, your controller will receive information from the vehicle model and determine how the transmission should behave. It will make decisions about gear selection and operating modes based on current vehicle conditions. 

## 3.2. What is a plant?
The plant is the system that you are trying to control. A "Plant" can be many things, such as a motor, a vehicle, a process in a factory. For this challenge, the plant is the vehicle model, which consists of the engine, 10-speed transmission, and vehicle dynamics.

## 3.3. What are requirements?
Requirements are the specifications that your control system must meet in order to be considered successful. These requirements will be derived from the system specification provided later in this document, and will include things like functional targets, safety requirements, performance metrics, and other constraints that your control system must adhere to. It will be your responsibility to review the provided requirements, identify missing requirements, and create additional requirements to ensure that the control system is robust and meets the needs of the vehicle.
Things to keep in mind when writing requirements:
- Requirements need to be precise, unambiguous, and clear
- Requirement should be implementation agnostic, meaning that they should not specify how the control system should be implemented, but rather what the control system should do. This will allow for more flexibility in the implementation of the control system and will allow for different approaches to be taken to meet the requirements.

An example of how requirements should be written: 
"The **<system/component>** shall **\<specification>**. **\<rationale>**"


- System/component: The requirement needs to be applicable to a system or component.
- Specification: The specification is the criteria that need to be achieved. The specification shall be specific (not ambiguous or easily confused with a different interpretation) and is usually quantifiable.
- Rationale: The rationale is the reason why the requirement exists. The rationale can be a direct source, or an explanation of why the specification is required.

## 3.4. What is testing? (The verification process)
Often referred to as a "test case", testing is the process of verifying that your control system meets the requirements that have been set forth. This will be done by running simulations of your control system and comparing the results to the expected behavior as defined by the requirements. You will be provided with a few test cases to get you started, but it will be your responsibility to come up with additional test cases to ensure that your control system is robust and meets the requirements. A good rule of thumb to start with is to have at least one test case for each requirement, and to have a few additional test cases that cover edge cases and unexpected behavior.

# 4. The Challenge: Implementation of a Controller for a 10-Speed Automatic Transmission
Now that you have your development environment setup and a basic understanding of what controls are and how they work, let's dive into the challenge itself. You have been provided with a model of a gas-engine longitudinal vehicle with a 10-speed automatic transmission, along with a system specification describing the desired behaviour of the controller. A starter set of requirements derived from this specification is provided in Transmission_Requirements.slreqx. Your task will be to implement a control system that will determine when to shift gears based on feedback from the vehicle dynamics, as well as other factors such as engine load and throttle position. The control system will also need to ensure that the vehicle operates within certain performance requirements.
During the challenge, there are three main types of files you will need to work with: simulink files(.slx), requirement sets(.slreqx), and test suites(.mldatx). Start by familiarizing yourself with the system model which contain the input, controller, plant and feedback loops. You may make any modification you would like to the controller, but you are not allowed to modify the plant or feedback loops. The requirements document will contain the specifications that your control system must meet, and the test cases will be used to verify that your control system meets those requirements.

## 4.1. Stage 1: Requirements Development
Before beginning development of any piece of software, it is important to first define the requirements of the software so that the software can be strictly evaluated through test cases to ensure that the software behaves as intended. Below is a description of the desired behaviour, from which we derived a few sample requirements that you can find in the transmission_requirements.slreqx file. Use the below system specification to guide your requirements, but don't be limited to what is laid out here, you are welcome to add additional specifications that you feel are important to the system.

Your first task will be to create additional requirements for the system, below is a rule of thumb of what number to aim for to start depending on your groups max year:
- 1st year: 2-4 additional requirements
- 2nd year: 4-6 additional requirements
- 3rd year: 6-8 additional requirements
- 4th year: 10+ additional requirements

### 4.1.1. System Specification for the Challenge
Below is the specification of the controller you need to design, this is where the example requirements in the Transmission_Requirements.slreqx are derived from. These are split into basic and advanced system specifications. We recommend first deriving and implementing requirements from the basic specifications before progressing to the more advanced specifications.
	
#### 4.1.1.1. Base System Specification
- Always keep the engine safe and within its operating limits of between 800 and 7250 RPM.
- Transmission: (A complete stop is defined as the absolute vehicle speed being less than 0.1m/s)
    - Ensure that the driver can only shift into park when the vehicle is at a complete stop.
        - When the driver shifts into park under valid conditions the parking actuator shall be commanded to close within 100ms.
        - When the driver shifts out of park, the parking actuator shall be released within 100ms.
    - The driver can only shift into drive when the vehicle speed is greater than -0.5 m/s.
    - The driver can only shift into reverse when the vehicle speed is less than 0.5 m/s.
    - Neutral gear operation shall be permitted regardless of vehicle speed.
#### 4.1.1.2. Advanced System Specification
- Target vehicle acceleration performance is 0-60 mph in less than 12 seconds.
- The controller should prioritize operating the engine near its peak efficiency whenever doing so does not conflict with safety or drivability objectives.
- The driver's throttle command may only be modified by the transmission for at most 1s (i.e. during a shift to help match the rpm of the engine to the rpm of the transmission).

**Note: Third/fourth-year students are expected to attempt to optimize one of the listed specifications, but teams can try to optimize as many as they would like.**

## 4.2. Stage 2: The Controller
Implement a control strategy within Transmission_Controller.slx that satisfies all requirements documented within Transmission_Requirements.slreqx while maximizing vehicle performance, efficiency, and drivability.

## 4.3. Stage 3: The Testing
This will be the section of the challenge where you will update the `Transmission_Test_Suite.mldatx` test suite file to create test cases that verify the existing requirements as well as the one you have created. Look through the [Requirements and testing](Guides/RequirementsAndTesting.md) guide to see how to set up and create test cases. 

The test cases you will be creating will need an input (.mat) file to use to evaluate the test, thus it is expected that you create your own inputs set. You can use the existing inputs sets ([ETRS_DriverLog.mat](InputFiles/ETRS_DriverLog.mat), [ETRS_0-60Log.mat](InputFiles/ETRS_0-60Log.mat)) and modify the signals to represent the situation that your test case is evaluating, so it is strongly recommended that you make a copy of the one of the existing input sets before modifying.  If a requirement states, "the gear selector shall only command PARK if the vehicle speed is below 0.5 m/s", the input must be driven so that the controller behaviour can be triggered to execute the required behaviour.  The purpose of the test suite is to verify that the controller satisfies the requirements documented in Transmission_Requirements.slreqx. Essentially, we are asking that you create a system that constantly checks if certain behaviours are implemented within your controller. An example of this style of logic would be, if variable X is equal to 3 and variable Y is equal to 7, then I expect variable Z to be equal to 5. Be sure to allow a buffer of around 10-20 milliseconds delay for each logical and temporal assessment.

There is an existing test case file `Transmission_Test_Suite.mldatx` that already contains two tests cases for requirements 3.1 and 4 as example you can use as a reference. It is expected that each team must create valid test cases for the remaining initial requirements that were given to each team in `Transmission_Requirements.slreqx`, along with the team-derived requirements. 

You will also be expected to observe key metrics to determine the results of any optimization methods. Please refer to the [setup](Guides/DevEnvironmentSetup.md#4-running-the-model) guide to understand how to run simulations and observe the data across the simulation. There will be two different data sets that will test the acceleration time (0-60 mph) and the vehicle efficiency/drivability. It is recommended you take screenshots of the graphs for these metrics (i.e. time, vehicle speed, efficiency, etc.) along with the relevant input signals. This is so over the course of the optimization process so you can link it back to different methods you may implement, making it easier to understand the work that was done. 

## 4.4. Extra Credit
- Git [Best Practice](https://about.gitlab.com/topics/version-control/version-control-best-practices/) - It's ok if you don't exactly follow this, as long as valid justification is provided, not just "It was easier" or "I couldn't figure it out"
- Edge cases - The provided input sets only cover a small subset of the correct possible inputs that the controller may encounter. It is your responsibility to come up with additional test cases that cover edge cases and unexpected behavior. This will ensure that your control system is robust and can handle a wide range of inputs. 
    - For example, an input set that covers some improper drive behavior such as attempting to shift into a gear that would cause the engine to exceed its safe operating limits, or attempting to shift into a gear that is not available in the current transmission state. These types of inputs will help to ensure that your control system is robust and can handle unexpected behavior.
- Shift quality (Drivability) optimization
- Fuel economy optimization
- Performance optimization

**Note: If you are having trouble with where to start on some of the extra credit items, please reach out to the individuals listed in the Closing Remarks section.**

# 5. Final Deliverables
Each team is expected to create a 10-15 minute science fair-style presentation to present your work to a panel of evaluators (they are nice people, don't worry 😊). All teams will be required to attend the presentation session at McMaster Automotive Resource Centre (MARC). If your team cannot make it in-person to the presentation session, please contact the individuals listed in the Closing Remarks section. This presentation should focus on outlining the task, the requirements the team derived, the functionality of the team-developed controller, key concepts implemented to achieve said functionality, results from controller implementation through key metrics (i.e. acceleration time, efficiency, etc.), and any major roadblocks the team faced throughout the challenge. At the end, there will be roughly 5 minutes where evaluators will be asking questions, so be prepared. 
All requirements, controller, and test development must be completed before the submission date. This will be enforced by observing the commit history within each team's git repository. However, this does not mean that an incomplete project autmatically disquilifies a team from consideration, as the intention of the challenge is to observe how each team approaches it, their willingness to attempt to solve it, and to extract lessons learned. 

**Note: Third/fourth-year students are expected to provide optimization results through the previously mentioned key metrics and the optimization process/decisions within the presentation as well.**
## 5.1. Submission
To submit your work, submit a pull request back to the main development challenge repo you initially forked from. Ensure that your pull request includes all requirement files, controller updates, test cases, input files, and team contribution documentation before submission.
## 5.2. Key Dates
- October 2nd 11:59PM: Submission is due (Requirements + Controller + Test)
- October 4th 1:00PM: Presentations at MARC

# 6. Closing Remarks
Please feel free to reach out to me (sticklat@mcmaster.ca) or use the PCM Development Challenge channel on Teams if you have any questions or to check in. Good luck and have fun!

