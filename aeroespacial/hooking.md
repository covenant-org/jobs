# Hooking

Currently we're building a tower in which the drone will return from
flight missions to charge its battery, download all of its data to the
computer in the tower, etc.

The landing currently looks like:

[![Watch the video](/assets/preview-short.jpg)](/assets/landing-short.mp4)

The goal of this challenge is to have a system in which the drone is able to
do the hooking on its own (without the need for human intervention) based on
the input from the different sensors of the drone.

One of the proposed solutions from our team was to use [ArUco](https://docs.opencv.org/4.x/d5/dae/tutorial_aruco_detection.html) 
which would allow to have a reference about the distance from the drone
to the actual location of the hooking zone and then the system would do its job
until the tower grabs the drone (closes the internal mechanism).

For the implementation you can use the one mentioned above or start from something
on your own, it's up to you!

We're currently using the [Pixhawk 6X](https://docs.px4.io/main/en/flight_controller/pixhawk6x.html)
in the drone that appears on the video. Ideally the solution you propose will be
ready to test directly on the drone. For the connection to the drone to allow for
external control of it we're using the [MAVSDK](https://mavsdk.mavlink.io/main/en/index.html)
and you can find some examples of code we have already develop [here](https://github.com/covenant-org/openpilot/tree/master/tools/sim/bridge/gz/mavlink).

To be able to test your algorithm you can use the simulator that PX4 [offers](https://docs.px4.io/main/en/simulation/).

You can [ask us](mailto:eduardo@nuclea.solutions) by sending an email to share with you the
prototype from the tower.
