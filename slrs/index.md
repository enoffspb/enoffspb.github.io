Hello everyone. It is a project about developing an AI system for recognition sign languages.
Also, it is a workbook about Object-Oriented Analysis and Design.
This project is based on the knowledge received at work in Surdo Online, IT-V LTD, with CEO - Alexey Melnik
and a programmer - Ilya Pastukhov, an author of that blog.
R&D has been completed with the support by national science programs and grants of Russia.

We won't talk about what is a sign language so much, you can find all the info on the web.
The important things for us are the following:
 - There are two modes of speaking: gestures and dactylemas.
 - A form of hand is an essential property.
 - Emotions and body moves are important too for semantic analysis, but not for early stages of development.
 - For distinct countries and regions languages are different.

And our scene likes this:
![a scene image](../images/surdo-scene.png)
*the photo is from surdoserver.ru*

The main aspect of the suggested solution is an architecture of the system.
We are going to build a difficult complex system, thus, we need to use techniques and best practices
from OOAD and enterprise development. On the other hand, it may be just a combination of one modern NN
and a big dataset, but not in this case. We need to have a lot of control of a recognition process, we need to build stable intermediate releases step by step,
and we must give guarantees that our solution will be able to work in production in the future.

Let's start to decompose the system into high-level modules.
First, split the recognition process into two independent parts:
 - gesture recognition from a video source
 - semantic analysis of recognized data
 
We aren't considering semantic analysis right now, simply, it will be sign => word mapping.
Out target is recognition gestures from a video, let's call it **RecognitionComponent**.
Well, there is an input. Generally, it may be a remote real-time video, a webcam stream, or a file with a recorded video, but it is not a concern of the component.
Instead, we define a basic **InputInterface** like this (pseudocode):

```
interface InputInterface {
    addFrame(frame);
    setFps(fps);
    getFps();
}
```

Any external client can pass a video frame by frame to the component. Without set FPS an input is parsed as a real-time source,
otherwise, the component expects a video with static FPS.

Next, we need to get recognition results. It is a **OutputInterface**.
```
interface OutputInterface {
    onRecognize
}
```

The output must return results in a defined format, at the start it will be like this:
```
struct RecognizedElement {
    int id, // or it may be "code", for example
    char* code
    
    // further there will be additional values and flags
}
```

And finally, we need an output video. There are some reasons:
- visualize a recognition process
- merge internal streams into one output
- add controls and text information
- make reports with results of testing for a control department

It will be a **DebugOutputInterface** with the following an external interface:
```
interface DebugOutputInterface {
    onFrame
}
```
About its internal interface we'll talk later.

In a result we have defined clear boundaries of the component and have stated it with a UML diagram.

[![a UML diagram](../diagrams/RecognitionComponent.1.drawio.png)](../diagrams/RecognitionComponent.1.drawio.png)

