In C++, a ****union**** is a user-defined data types that allow you to store different types of data in the same memory location but unlike structures, where each member gets its own memory, union members share the same memory space making unions more memory-efficient for specific use cases.

Real Life example
- **Event Occurs:** An event happens outside your program (e.g., a mouse click). The operating system or an underlying library (SDL in this case) detects this event.
    
- **Event Data is Populated:** The library populates a single `SDL_Event` union with the data for that specific event type. For example, if a mouse button is clicked, the library will write to the `button` field of the union. It also writes the corresponding event type (e.g., `SDL_MOUSEBUTTONDOWN`) to the `type` field. All other fields in the union are now invalid.
    
- **Your Program Polls for Events:** Your program calls a function like `SDL_PollEvent()` to check for new events. This function gives you the populated `SDL_Event` union.
    
- **You Check the Type:** You, as the programmer, are responsible for checking the `type` field first. You use a `switch` statement on the `event.type` member to determine which union member is currently valid. For example:

```cpp

typedef union{
  Uint8 type;
  SDL_ActiveEvent active;
  SDL_KeyboardEvent key;
  SDL_MouseMotionEvent motion;
  SDL_MouseButtonEvent button;
  SDL_JoyAxisEvent jaxis;
  SDL_JoyBallEvent jball;
  SDL_JoyHatEvent jhat;
  SDL_JoyButtonEvent jbutton;
  SDL_ResizeEvent resize;
  SDL_ExposeEvent expose;
  SDL_QuitEvent quit;
  SDL_UserEvent user;
  SDL_SysWMEvent syswm;
} SDL_Event;

SDL_Event event;
while (SDL_PollEvent(&event)) {
  switch (event.type) {
    case SDL_QUIT:
      // handle quit event; the "quit" member is valid
      break;
    case SDL_KEYDOWN:
      // handle keydown event; the "key" member is valid
      break;
    case SDL_MOUSEBUTTONDOWN:
      // handle mouse button event; the "button" member is valid
      break;
  }
}
```