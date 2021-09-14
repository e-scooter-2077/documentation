# Device Emulator Requirements

1. Emulate all the devices on a Iot Hub instance
    1. Fetch the list of all devices
    1. For each device, start an infinite emulation that lasts until a "stop event" is fired
        1. A stop event can be a keyboard interrupt or a C# method call
        1. The emulation consists of:
            1. Load device data
            1. Decide which property should be changed, why and how
                1. If the device is not in-between a trip, start a trip randomly
                1. If it's in-between a trip, randomly select an action:
                    1. End the trip, lock the device
                    1. Change position based on velocity
                    1. Change position and change velocity
                1. If it's in-between a trip, consume some battery
                    1. If the battery is below the power saving threshold go in power saving mode
                    1. If the battery is below the standby threshold go in standby mode
            1. Change the property values
            1. Submit the update to IoT Hub

    !!! note Emulation
        Emulating a device means firing a sequence of realistic updates on its reported properties on IoT Hub. We consider every device to be an **E-Scooter**.
1. Can be executed both as script and as a library