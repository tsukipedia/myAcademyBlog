---
category: Essays
title: Week 13 & 14
date: 2023-06-19
---

Over the past two weeks at Encora, my exploration with Java’s object-oriented concepts has been exhilarating, especially as I could exercise my newfound knowledge through the Music Advisor project. A surprising revelation was the existence of anonymous functions and classes in Java, a concept I was familiar with through lambda functions in JavaScript/TypeScript. Their applicability in an object-oriented program seems limited, but just knowing they exist in Java is empowering, I'm certain they'll find their way into my future codes.

The concept of Java abstraction, though likely covered in my university lectures, had always eluded me. However, Hyperskill swooped in with its practical examples, and I grasped the utility of abstract classes - I even waved goodbye to some if-else/switch-case statements thanks to these!

Another sweet nugget of knowledge was the use of generic types in Java. Although the examples on Hyperskill were outdated, it nudged me to venture out and hunt down the modern approach. Even if my aversion to un-typed variables is well-documented, I must admit that polymorphism does make them rather handy.

Simultaneously, I dived into the realm of clean code for my lightning talk, gobbling up opinions and best practices for code readability. University education had me declare variables at the beginning, but Robert C. Martin, in his book 'Clean Code' (aptly known as the clean code bible), advocates for variables to be declared near their point of usage - something that resonated with me deeply.

Another enlightening takeaway on clean code was the vertical separation of concepts, and I was particularly struck by an example:

``` JSX
// Dirty code
const SomeReactComponent = ({ meeting, userId}: Props) => {
    return (
        <div>
        <h2>Some title</h2>
        <p>Some text.</p>
        <div>Some element.</div>
        {new Date() < new Date(meeting.startTime) &&
            (userId === meeting.creatorId ||
                (!!meeting.permissions && meeting.permissions.canCancel)) &&  <button>Cancel Meeting</button>
        }
        </div>
    )
}
```

``` JSX
// Clean code
const SomeReactComponent = ({ meeting, userId}: Props) => {
    const meetingHasStarted = new Date() > new Date(meeting.startTime); 
    const hasCreatedMeeting = userId === meeting.creatorId;
    const hasCancelPermission = hasCreatedMeeting || (!!meeting.permissions && meeting.permissions.canCancel);
    const showCancelButton = !meetingHasStarted && hasCancelPermission;
    return (
        div>
            <h2>Some title</h2>
            <p>Some text.</p>
            <div>Some element.</div>
            {showCancelButton &&  <button>Cancel Meeting</button>}
        </div>
    );
}
```
Having stumbled across lengthy conditional blocks before, breaking them into explanatory variables has now become a best practice etched in my memory. 

Another clean code gem was favoring non-static methods over static ones. Now, confession time - I went a little static-happy in the Music Advisor project. However, this gave me invaluable insights into why moderation is key with the static keyword.

Lastly, I would be remiss if I didn’t mention the enriching lightning talks by my colleagues. Edgar's talk on the Django framework stood out. Being fond of Python but only using it for AI problems and basic teaching, web programming with Python was a puzzle. Edgar's talk was the missing piece, and I am excited to fuse web development and AI, my two passions, using Python.

In conclusion, these two weeks have been an odyssey of learning, relearning, and unlearning. The treasure trove of knowledge I've acquired has not only sharpened my skills but also widened my horizons.
