[Command](https://gameprogrammingpatterns.com/command.html) is a behavioral design pattern that turns a request into a stand-alone object that contains all information about the request

Link
Consider a PlayStation Controller which has 4 buttons x,square,circle,triangle.
Each of this button is assigned to a particular function in the game. Now to assign function to the button we can do 2 things.

Simple Implementation

```cpp
void InputHandler::handleInput()
{
  if (isPressed(BUTTON_X)) jump();
  else if (isPressed(BUTTON_Y)) fireGun();
  else if (isPressed(BUTTON_A)) swapWeapon();
  else if (isPressed(BUTTON_B)) lurchIneffectively();
}
```
How will the User configure the buttons according to their needs ??
--> Abstract out the commands

```cpp
class Command {
public:
	virtual ~Command(){}
	virtual void execute(Character& c) = 0;
};

class JumpCommand: public Command {
	public:
		virtual void execute(Character& c) {
			c.jump();
		}
};

class FireCommand: public Command {
	public:
		virtual void execute(Character& c) {
			c.fire();
		}
};
```

How the abstraction would work?
```cpp
// --- Initial Setup (e.g., at game start or level load)
void setupPlayerControls() { 
	buttonX_ = new JumpCommand(); // Button X is assigned to Jump 
	buttonY_ = new FireCommand(); // Button Y is assigned to Fire 
}

Command* InputHandler::handleInput()
{
  if (isPressed(BUTTON_X)) return buttonX_;
  if (isPressed(BUTTON_Y)) return buttonY_;
  if (isPressed(BUTTON_A)) return buttonA_;
  if (isPressed(BUTTON_B)) return buttonB_;

  // Nothing pressed, so do nothing.
  return NULL;
}

// Game Loop
Command* command = inputHandler.handleInput();
if (command)
	command->execute(character)
```

Another Use case of the command Pattern is undo-and-redo operations. Without the Command pattern, implementing undo is surprisingly hard

```cpp

#include <iostream>
#include <ctime>
#include <vector>
#include <stack>

class Character
{
public:
    std::string name;
    int x, y;
    Character(std::string _name) : name(_name) {}

    void move(int _x, int _y)
    {
        x = _x;
        y = _y;
    }
};

class command
{
public:
    virtual ~command() {}
    virtual void execute(Character &c) = 0;
    virtual void unexecute(Character &c) = 0;
};

class move : public command
{
private:
    int x, y;

public:
    void execute(Character &c)
    {
        int randomx = rand() % 4;
        int randomy = rand() % 4;
        c.move(randomx, randomy);
        x = randomx;
        y = randomy;
        std::cout << "moved to" << x << "," << y << std::endl;
    }

    void unexecute(Character &c)
    {
        c.move(-x, -y);
        std::cout << "moved back to" << -x << "," << -y << std::endl;
    }
};

int main(int argc, char const *argv[])
{
    std::srand(std::time(0));
    Character *c1 = new Character("Shashank");
    std::stack<command *> commands;

    move *m1 = new move();
    m1->execute(*c1);
    commands.push(m1);

    move *m2 = new move();
    m2->execute(*c1);
    commands.push(m2);

    move *m3 = new move();
    m3->execute(*c1);
    commands.push(m3);

    move *m4 = new move();
    m4->execute(*c1);
    commands.push(m4);

    while (!commands.empty())
    {
        commands.top()->unexecute(*c1);
        commands.pop();
    }

    return 0;
}

OUTPUT:
moved to 0,1
moved to 3,0
moved to 2,2
moved to 3,1
moved back to -3,-1
moved back to -2,-2
moved back to -3,0
moved back to 0,-1
```

FUN FACT --> Redo may not be common in games, but re-play is. A naive implementation would record the entire game state at each frame so it can be replayed, but that would use too much memory.
Instead, many games record the set of commands every entity performed each frame. To replay the game, the engine just runs the normal game simulation, executing the pre-recorded commands