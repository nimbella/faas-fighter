# FAAS WARS

FAAS WARS is a battle between robot fighters. You create a fighter by
writing some code (as simple as a single function) and deploy the code
as a serverless API using [Nimbella](https://nimbella.com).

Your function is the API. You do not worry about creating a server to
run your fighter. With the Nimbella serverless cloud, you can focus on
creating the best FAAS Fighter, and you won't be distracted by
infrastructure concerns.

What can be better? Winning cash prizes!

Season 1 of FAAS WARS was a global success. We had 500 fighters from 7
countries compete and win $1,400 in prizes. You can read about the
[Season
recap](https://nimbella.com/blog/results-and-feedback-of-faas-wars-may-the-faas-be-with-you)
for more details.

We are back with Season 2 and new cash prizes. This time we partnered
with HackerEarth and this season is all about team battles. The
details are available
[here](https://www.hackerearth.com/challenges/hackathon/faas-wars-season-2/custom-tab/learning-station/#Learning%20Station).

# Starter Fighter

In this repo you will find a starter fighter project which gets you
going in less than a minute.

This project contains a serverless API that implements some basic FAAS
Fighters. You can deploy these fighters to your Nimbella account and
start battling.

Next, take it up a level by improving your algorithm. The
winning fighter strategy last season was surprisingly simple in
retrospect: scan back and forth along a particular axis, 
shooting lasers when you spot an enemy fighter, and only moving to
evade their lasers when hit.

The input and output requirements for API are described later on.

## 1. Install the Nimbella CLI

You will need the Nimbella CLI to get started. Please download the CLI
specific for your platform from the
[Nimbella website](https://nimbella-apigcp.nimbella.io/login.html?token=_#cli)
and install it by following the prompts on your screen.

## 2. Login using the CLI

Once you install the CLI, run the following command on your terminal.

```
  nim login
```

If you do not yet have an account, this will be your opportunity to
sign up (it's free and no upfront information is needed except your
e-mail or GitHub id). You do need a
[Nimbella account](https://nimbella.com/signup) to keep going.

## 3. Clone and own your fighter

You will likely want to edit the implementations provides in this
project. To clone the repo, run the following command.
```
  git clone https://github.com/nimbella/faas-fighter.git
```

There are several sample fighters included as reference.
Each of the fighters is self-contained in a single source file. We
provide reference implementations in
[JavaScript](./packages/default/JsBot.js),
[Python](./packages/default/PyBot.py) and
[Go](./packages/default/GoBot.go).

You can edit one of these or create your own using another language
that you prefer. Use your favorite IDE to do the coding.

When you're ready, deploy the fighters to make your APIs live. This
is done with the Nimbella CLI.

```
  nim project deploy faas-fighter
```

The project deploy command assumes your project is called
`faas-fighter` and is in the working directory where you run the
`nim` command from. You can specify the path to your project
otherwise (e.g., `nim project deploy /path/to/faas-fighter`).

The output from `nim project deploy` is similar to the output below.

```
Deploying project '/path/to/faas-fighter'
  to namespace 'example-namespace'
  on host 'https://apigcp.nimbella.io'

Deployed actions ('nim action get <actionName> --url' for URL):
  - GoBot
  - JsBot
  - PyBot
```

# Understanding the Fighter API

You are creating a serverless API to implement the logic for your
fighter.

- You may use JavaScript (Node.js or TypeScript), Python, Go, PHP,
  Java, Rust, Ruby, Deno, Swift, or .NET to implement your fighter.

- The API receives an **Event** as input and returns **Commands** as output.

- The fighter has an energy level that starts at a pre-determined level.
  The energy level decreases by one unit every time it is hit.

- A fighter is eliminated and the match is over when the energy level
  reaches 0.

- The match is also over (and your fighter is eliminated) if your API
  returns an error or throws an exception. Code carefully.

## The **Event**

The fighter receive a message in the following format:

```
{
  "event": "idle",
  "energy": 5,
  "x": 110,
  "y": 240,
  "angle": 23
  "tank_angle": 232,
  "turret_angle": 150,
  "enemy_spot": {},
  "data": {}
}
```

- The `event` can be:
  - `idle`: the fighter has its order queue empty and and asks for new orders
  - `enemy-spot`: the fighter has spotted the enemy and can hit him firing
  - `hit`: the fighter was hit by an enemy bullet
  - `wall-collide`: the fighter collided with a wall
- `x` and `y` are the position in the battlefield,
- `tank_angle` and `turret_angle` are the angles of the tank and of the turret in degrees.
- `angle` is the sum of the angle of the turret and the tank, modulo 360
- `energy` is your energy level,

When the event is `enemy_spot` there is also the field `enemy_spot` in this format:

```
{
    "id": 1,
    "x": 291,
    "y": 180,
    "angle": 23,
    "distance": 202,
    "energy": 1
}
```

The fields `x` and `y` are the enemy location, `angle` is the absolute angle to fire him, `distance` is its distance and `energy` the enemy energy level.

The `data` field is a field that you can set with your own values with the `data` command, to save a state for further actions.

## The **Commands**

Your algorithm must decide on a set of commands to issue for your
fighter. The result of the API is an array of commands (you can issue
multiple orders). For example:

```
[{
  "move_forwards": 50,
  "shoot": true
 },
 {
  "move_backwards": 50,
  "turn_turret_right": 180,
  "shoot": true
}]
```

Each command consists of a single sequential action and one or more parallel actions.
The sequential actions move your fighter, the parallel actions aim the
fighter's turret or perform other actions.

The sequential actions are:

- `move_forwads: <number>`: move forward of the given number of pixels
- `move_backwards: <number>`: move backwards of the given number of pixels
- `move_opposide: <number>`: move in the opposite direction of where you were moving - useful when you hit a wall
- `turn_left: <number>`: turn the tanks to the left of the given angle in degrees
- `turn_right: <number>`: turn the tanks to the right of the given angle in degrees

The parallel actions are:

- `turn_turret_left: <number>`: turn the turret to the left of the given angle in degrees
- `turn_turret_right: <number>`: turn the turret to the right of the given angle in degrees
- `shot: true`: fires a bullet; note you can fire up to 5 bullets at the same time
- `yell: <string>`: yell a message that will be displayed in the battle field

Each command should contain no more than one sequential action. If you
specify more than one sequential action, only one will be executed
(which one is executed is out of your control at that point).

You can read more about how to build a fighter [following this
tutorial](https://nimbella.com/blog/faas-wars-serverless-virtual-robot-competition?utm_source=subdomain&utm_medium=landing&utm_campaign=faaswars).

# Support and Feature Requests

We welcome your feedback and contributions.

- For general bugs and enhancements, we suggest opening [an issue on GitHub](https://github.com/nimbella/faas-fighter/issues/new).
- For quick questions, you can reach us in [Slack](nimbella-community.slack.com).
