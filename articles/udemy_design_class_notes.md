# Design Rules: Principles and Practices for Great UI Design

[course link](https://www.udemy.com/course/design-rules/)

## Ground Rules:  What You Need To Know Up Front

### Design Is Design Is Design

* "If you can design one thing, you can design anything." --Massimo Vignelli
* Important questions:
  * Does the design serve the **purpose** of its existence?
  * How should it be **perceived** and **received** by the audience?
  * Why does that **matter** to anyone?
* "If you don't have a good solution, you don't have a good problem."
* The principles of good graphic design are the same principles that dictate good UI design.
* Make sure every design decision you make is purposeful.  
  * Everything has a purpose or it goes.  Simplify everything down to its essentials.
  * "That looks cool" isn't a purpose.  It doesn't really do anything for anyone.
* "visual tension" == points where things touch.  Eye is drawn to visual tension.
* If a screen is data intensive, let the UI recede by using muted colors and wide spaces.
  * Pick out the critical points (search button, for example) with sharp colors but most of the rest of the UI elements should be muted to allow the user to see the data more clearly.

### Stop Solving Other People's Problems

* Don't look at themes online.
  * Themes look nice but they don't really give you a sense of what, how and why things were designed the way they were.
  * They're also unlikely to exactly fit what you're trying to communicate.
* Don't look for inspiration in the work of others. (ed:  Really?  Sell me on this one...)
  * What you find is someone else's solution to someone else's problem.
  * The problem you have to solve belongs exclusively to you, your client, and your users.
* "Do not seek to follow in the footsteps of the wise.  Seek what they sought." - Matsuo Basho
* Don't look at trends.
  * Don't adopt a **look** without considering whether it's **appropriate**  for the situation or whether it'll be understood by the audience.
  * Flat design for example minimizes the **visual affordances** making it difficult for users to understand what they're seeing.
* Design vs Decoration
  * Designer thinks about the appropriateness of the visual decisions being made, and informs those decisions with research.
  * Decorator relies on instincts as to what looks good and what doesn't.  But they don't do the research etc that goes into design.
  * Design is contextual.
* Stop solving other people's problems.
  * Much like stand up comedy, don't try to deliver someone else's material.

### Form doesn't and shouldn't follow function.

* This concept has been misinterpreted, misapplied, and watered down over the years.
* Design process following this rule of thumb:
  * Gather requirements
  * Determine aesthetics based on those functional requirements
  * Fails to deliver what's really needed
* Louis Sullivan, 1896:
  * It is the pervading law of all things organiz and inorganic, of all things physical and metaphysical, of all things human and all things superhuman, of all true manifestations of the head, of the heart, of the soul, that the life is recognizable in its expression, that form ever follows function.  This is the law."
  * No true Scotsman much?  ;-)
  * What he was saying is that tradition doesn't matter as much as the needs of the things we are designing.  (Steel Skyscrapers guy, big architect.  He was rebelling against traditional Greek and Roman architecture.)
  * Frank Lloyd Wright was his apprentice.
    * Believed form and function should be united.
    * Guggenheim museum has one path which can be walked straight forward in a spiral.
  * Gropius:  "Architecture begins where the engineering ends."
    * Form reflects function.
    * Communication and meaning vs decoration.
    * Artistic devices are there for usability.
  * Principles:
    * color and shape
    * typography
    * spatial relationships
    * harmony, balance, rhythm, proportion, symmetry
  * Get a copy of Universal Principles of Design

### Balancing Form and Function:  Prescription vs Description

* Understand **why** you make the visual decisions that you make.
  * Description:
    * Beauty results from purity of function (not ornamentation). 
    * Forms in nature compete to survive.
    * So do products in the marketplace (be it external or internal.)
  * Prescription:
    * Aesthetic considerations in design should be secondary to functional considerations.
    * Programmers and engineers fall prey to this one.
    * To be fair, it's what we were taught.
    * "What elements here don't specifically serve a function, remove the ones that have no function."
      * This isn't a TERRIBLE rule of thumb.  BUT... ergonomics, usability, contextualization, differentiation in the marketplace... these are all functions.  They're just not the "technical" function. 
    * Taken to its logical and absurd conclusion, everything would be designed the same way. 

### Why Form Follows Function is NOT a UI Design Prescription

* Implementation model
  * Reflects technological needs.
* Mental model
  * Reflects the user's understanding
* Represented model
  * Bridges the gam between the implementation model and the mental model.
  * Or in other words, the design brings the technology to the user **where they are.**
  * Make the represented model be as close to the mental model of your user base as possible.
  * If you have multiple types of user, you need multiple representation models.
* Think about cars:
  * Do you need to be a mechanic or an engineer to drive?
  * Nope.  You need to know your speed, whether or not there are malfunctions going on, how to steer, go faster, slow down.
* Form should be determined by **success criteria**.  Outcomes, not features.  Can the user do the things they want to do, and see the things they need to see?
* **Convenience trumps best practice every time.**
* Questions:
  * What are the features, functions, and form that are critical to success?
  * What can you trade off which would least harm the success criteria?
  * Aesthetics and functionality are both on the table for this.
* Function is a single, isolated aspect of what drives and influences form.

### "Every force evolves a form." -- Mother Ann Lee (Founder of Shaker movement)

* Audience needs
* Client desires
* Ethical obligations
* Aesthetic inclinations
* Tech constraints
* Cultural presuppositions
* Functional requirements
* Material properties (screen size, interface...)
* Available time
* Available budget
* Audience skill levels/constraints/access limitations

### Less is More:  Small Screens, Big Challenges

* "Should over could."
* Visual complexity is magnified on a small screen.
* Overly complex visuals can drive a user away in as little as 50 milliseconds.
* Perception of complexity => assumption of dificulty to learn/user.
* Concise gestures are powerful.  Ruthlessly edit your small-screen representations.
* BUT:  Don't go so far as to minimize your **affordances**.  Make sure your buttons look like buttons etc.  
* "Don't make me think."  This goes back to the mental model:  make your representation model is as close to the user's mental model as possible.
* Unlabeled icons are a tricky point, because if the user doesn't instinctively speak that icon's language, they might never touch it.

### Rules for small screen design

* Focus on context of use:
  * When do users use the app?
    * Stressed?
    * Bored?
    * Busy?
    * Lost?
* Simplify.  Each screen gets ONE primary action.
  * Every screen should be userful, meaningful, valuable.
* Number of screens/steps doesn't matter.  What matters is the value provided by the clicks/taps/interactions.
  * This is the Single Purpose Principle in a UI context.
* Design for thumbs, not fingers.
  * Most people use their thumbs.  Actions on the BOTTOM not the top.
  * Also handles for fat fingers.  :)  9mm/48px minimum.
  * Provide ample space between targets to minimize mis-taps.
* Minimize the need to type wherever possible.
  * Use dropdowns, predictive searches, autocomplete
  * Remove unnecessary fields

## Organizing Visual Information

### Balance:  Creating VIsual Order

* Balancing is about arranging **positive** elements and **negative space** so that no one area overpowers the others (or at least not on accident.)
* Everything works together, without the elements competing with each other.
* Causes a missing the forest for the trees situation.
* Visual hierarchy can be damaged by an imbalanced design.
* Draw a wireframe of an existing interface by focusing in on the positive elements.
  * If the elements don't hang together, tightening the negative space might help.
  * Cut the number of visual groupings via balancing.  Fewer visual groupings == less overwhelming design.

### Case Study Notes

[Link to lecture](https://www.udemy.com/course/design-rules/learn/lecture/10321586#overview)

**Balance creates order and signals relationships.**

* Consistency in padding, column widths makes things more clear.
* Always be thinking about the relationship between elements.  
  * Elements that belong together should have tighter negative space between them.
  * Groups should be separated by looser negative space to make them very clear which elements belong to which group.
  * Make your differences in negative space large enough to be obvious that the difference is deliberate.
* Too many horizontal/vertical rules == visual clutter.  White space is less cluttered.
* In content-heavy contexts, balancing negative and positive space is critical.
* White space channels lead the eye through the layout.

### Rhythm:  Establishing and Reinforcing Comprehension

* Rhythm concerns the intervals between elements.
* Predictable size/shape/length leads to smooth rhythm.  
  * Consistency and repetition.
* Two or more of anything implies a related structure.
* Again, make sure that things that are similar are precisely similar, and things that are different are markedly different- there should be no ambiguity.
* Each group of elements has its own rhythm, and it's ok if they're different.
* Rhythm communicates relationship.
* If you create a structure, you are bound to maintain it.
  * The more complex the interface, the more control you need to maintain.

EVERYTHING communicates relationship.

### Harmony:  Shaping the parts into a whole

* Good harmony comes from rhythm and repetition.  
* Related elements should have agreement in:
  * Alignment
  * Shape
  * Color
  * Rhythm
  * Typography
* Another way to describe harmony is a visual echo between elements.
* Zebra striping is a good example of harmony in repetition.

### Using harmony to create directional flow

* Follow the cultural flow of text.  In English (and other european languages), left to right, top to bottom.
* A headline in one element can draw the eye to a call to action in another element.
* Use visual anomalies in a **purposeful** way to call attention to action elements.  (Buttons, etc.)
  * Treat different things differently.

### Dominance:  Directing and maintaining user focus

* Negative space can be something other than whitespace.  It can be a muted background similarity.
* Contrast creates dominance.
* Remember:
  * **C**ontrast
  * **R**epetition
  * **A**lignment
  * **P**roximity
* Dominance creates an entry point in your design.  It indicates where the user should start.
* Dominance decreases with the addition of elements:
  * Primary, secondary, tertiary dominance all deserve attention.
  * Hierarchy of content guides the user.
* Primary dominant element demonstrates what is important.
* Modal popovers use shading and opacity to create both relationship to and dominance over the content underneath.
* If there's no clear dominance between elements, they clash with each other.
* Be mindful of this hierarchy to reduce cognitive load on your users.
* If everything is special, nothing is special.
  * CHOOSE YOUR PRIORITIES MINDFULLY.
* Users will abandon ship if they don't have a clear idea of where to start.
* Inverse pyramid also applies to this.

### Dominance: Size, Negative Space, and Contrast

* Contrast creates dominance.
* Framing, white space, color, and size are all important ways to make contrast.
* **ONE PRIMARY DOMINANT ELEMENT PER PAGE**
* Avoid loops of flow

### Alignment:  Leading the eye across the screen

* Alignment is often the low hanging fruit of redesign/design fixes.
* Poorly aligned layouts fight your user at every stage.
* Be conscious of both left and right alignment.  Full justification wherever it helps to keep the cohesion of the layout groups.
* A lot of time, boxes and rules create noise where negative space would suffice.
* Align by context.
* Eyes are drawn to faces, specifically eyes and mouths.  You can use these for harmonious alignment.
* You can align baselines with each other, or with top lines, or midlines.  Similar principle for vertical alignment.

**Align everything with everything else.**

### Proximity

* Close proximity == related elements.  Distant proximity == unrelated elements.  Negative space.
* Proximity often beats color and contrast.  
* Loosen proximity to create separation.  Tighten proximity to create grouping.
* Organize related content with proximity.

## Using Color and Contrast Appropriately

### Color:  Getting attention and communicating emotion

* Colors need to be used consistently to avoid confusion.
  * To avoid recompiles!
* Symbol + color, repeated, delivers meaning.
* Contrast grabs attention.
* Branding via combo of color and icon.
* Breaking patterns also grabs attention.
* Desaturating color can allow one color to pop out more for attention.
* Colors can also, along with art style, suggest a time period.

### How Color Influences Interaction

* Twitter:  Consistent use of light blue for interaction cues.
* Twitter:  State change -> red, because different meaning == different color.
* Color can create continuity.  
  * Example:  Clicking a card with a red background could lead to a page with a red background, indicating connection.
* Color meanings are impacted by culture.  Select color schemes appropriate to your audience.
* Color shouldn't be the only differentiation between things in the UI.  High contrast is necessary to help people with color blindness.  Shape can help too.
* Muted/light/desaturated colors are best for backgrounds.
* Color is an accent.
  * One dominant neutral color.
  * One accent color.

### Color Theory

* Color theory is cool but takes a LOT of time and effort to understand and apply.
* Use colors sparingly.  
* Colors reinforce hierarchy and content.
* Colors need to be used consistently.
* Color should be used functionally, not decoratively.
* Functionality should **never** depend on color.
* Ask yourself:  Would this be just as functional for someone with color blindness?
  * Color + shape can help.

### Choosing the right colors for your UI:  Common associations

| Color  | Meaning                                                      |
| ------ | ------------------------------------------------------------ |
| Red    | Alarm, urgency, attention, intensity, warning of danger, love |
| Black  | Authority, power, timelessly cool, countercultural           |
| White  | Innocent, purity, peace, sterility, cleanliness              |
| Pink   | Romance, gratitude, grace, admiration, harmony, compassion, femininity (bleh) |
| Blue   | Peaceful, tranquil (oceans, sky) ,business, technology, innovation, masculinity (bleh) |
| Green  | Nature, organic, calming, refreshing, relaxing               |
| Yellow | Optimism, happiness, warmth, sun, positivity, joy, hope      |
| Purple | Luxury, royalty, wealth, luxury, sophistication, romance     |
| Brown  | Nature, earth, home, friendship, richness, genuineness, solidity |

Black, white, and red have the highest contrast.

These associations are NOT universal.  But they're a good place to start.

## Designing with Typography and Imagery



## Creating and Simplifying Visual Cues



## Things To Remember
