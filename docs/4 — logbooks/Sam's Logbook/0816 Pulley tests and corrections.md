I got part way through drawing up the platter for my [most recent head mechanism concept](0812%20Yet%20another%20head%20mechanism%20concept.md). It's not quite done yet, but I did want to see it in person and give the belt a test fit.

![](Screenshot%202024-08-16%20at%2011.27.36.png)

I made a print of it. It fits on the shaft of the stepper. There is a small amount of play but it's not overly severe. The space in the middle isn't quite big enough for the small pulley. (I've got a collar in there instead just for the photoshoot. I've used a spring on the underside of the shaft to retain the pulley against the collar.)

![](IMG_1514.jpeg)

Unfortunately, the belt doesn't quite fit. It feels as though there's about one tooth too many. I found [a white paper](https://assets.gates.com/content/dam/gates/home/knowledge-center/mectrol/whitepapers/belt-theory06sm.pdf) by belt designer Gates, that provides some mathematical identities, and I've used these to try to work out a correction factor for my next print.

![](IMG_1512.jpeg)

![](IMG_1513.jpeg)

I made the required modifications to the CAD file and two more 3D prints. The first was with $u=0.3\text{ mm}$. This pulley was markedly better and there were no skips, however it seemed that maybe I had corrected it slightly too much as the belt was a little loose in parts (implying the OD was a little smaller than it should have been, with $u$ a tad big). I therefore printed a second one at $u=0.25\text{ mm}$. This one shows pretty convincing alignment and a good fit without any tension on the belt. Arguably a little bit of preload tension might be desirable, and therefore maybe 0.25 mm is still a little too much, but it's definitely workable.

In summary, the pitch diameter should be bigger than the outer diameter of the pulley by approximately $2u=2(0.25\text{ mm})$ for a good neutral fit. (Note this was all tested with a pulley roughly 95 mm in diameter, but I expect the same relationship and math to hold for pulleys of any diameter.)