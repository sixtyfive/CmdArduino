# CmdArduino

CmdArduino is a small library to parse commands from the Serial port or anything else which implements the Stream API.  It is based on the original by Akiba at Freaklabs.org

To use it create your command functions, then wire them up in your setup function. All command functions must take `int arg_cnt, char ** args` as it's parameters and return `void`. Here's an example from a simple robot:

```cpp
void left(int arg_cnt, char **args) {
    LeftMotor->run(FORWARD);
    delay(200);
    LeftMotor->run(RELEASE);
}

void right(int arg_cnt, char **args) {
    RightMotor->run(FORWARD);
    delay(200);
    RightMotor->run(RELEASE);
}

void setup() {
    Serial.begin(9600);
    cmdInit(&Serial);
    cmdAdd('left', 'l', left);
    cmdAdd('right', 'r', right);
}
```

Call `cmdInit` with a pointer to the stream you are using. Call `cmdAdd` to attach a string command (long name as well as short name) to the command function.  Now you can type `left` or `right` (or `l` or `r`) into your serial port to command the robot.  The serial port will be parsed using newline, `\n`, as the delimiter. The command name should be the first item and any arguments after that.  For example, to tell the robot to turn left for three seconds, type in:

```
left 3000\n
```

Then the "left" function becomes:

```cpp
void left(int arg_cnt, char **args) {
    int time = 200;
    if(arg_cnt > 0) {
        time = parseNum(args[0]);
    }
    LeftMotor->run(FORWARD);
    delay(time);
    LeftMotor->run(RELEASE);
}
```
