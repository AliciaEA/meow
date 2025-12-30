<main>
    <!-- Hero -->
    <section class="hero">
        <div class="container">
            <h1>Meow Cat</h1>
            <p>
                The world's second cutest animal, little cats. Discover them,
                get to know them, find them cute. Welcome, to Meow Cat
            </p>
            <a href="./play" class="btn">Skip and Play</a>
        </div>
    </section>

    <!-- Intro -->
    <section class="intro container">
        <img src="./images/cute-cat-intro.webp" alt="cute cat" />
        <div class="text container">
            <h2>Meet a Cute Meow Meow</h2>
            <p>
                Just for reference, when I say 'Meow Meow', I'm talking about a
                cat. Just as a child when he says Broom Broom, and we know he is
                talking about a car. Let me introduce you to the world of cute
                meow meow
            </p>
        </div>
    </section>

    <section class="info">
        <h2>Information</h2>
        <div class="grid">
            <div class="mini-fact">
                <span>🐾</span>
                <h3>Retractable Claws</h3>
                <p>Silent stalking</p>
            </div>
            <div class="mini-fact">
                <span>🐱</span>
                <h3>Vertical pupils</h3>
                <p>Judge distance accurately</p>
            </div>
            <div class="mini-fact">
                <span>〰️</span>
                <h3>Whisker Sensors</h3>
                <p>To navigate in the dark</p>
            </div>
            <div class="mini-fact">
                <span>🍖</span>
                <h3>Carnivores</h3>
                <p>They cannot be vegetarian</p>
            </div>
        </div>
    </section>

    <!-- Question-like info -->
    <section class="whole-paragraph">
        <h2>
            Where do domestic cats originally<span id="Pink"> come from?</span>
        </h2>
        <p>
            Their ancestors are Near Eastern wildcats [Felis lybica]. They were
            frist domesticated in the Near East around 7500BC to help protect
            grain stores from rodents
        </p>
    </section>

    <section class="whole-paragraph" id="second">
        <h2>
            How much of their lives do cats spend<span id="Pink">
                sleeping?</span
            >
        </h2>
        <p>
            On average, cats senf about 70% of their lives asleep. This helps
            them conserve energy for the short, intnse bursts of hunting (or
            playing) they do throughout the day.
        </p>
    </section>

    <!-- Facts -->
    <section class="grid container" id="paragraphs">
        <div class="card">
            <h3>Habitat</h3>
            <p>
                Domestic cats are highly adaptable and can be found on every
                continent except Antarctica.
            </p>
        </div>
        <div class="card">
            <h3>Social Structure</h3>
            <p>
                While often thought of as "Loners", many cats are actually
                social. In the wild, females often form "colonies" to raise
                kittens together.
            </p>
        </div>
        <div class="card">
            <h3>Meow</h3>
            <p>
                Adult cats rarely meow at other cats; they primarily use this
                vocalization to communicate specifically with humans.
            </p>
        </div>
    </section>

    <section class="circles container">
        <h2>Fun Facts</h2>
        <div class="grid">
            <div class="circle">
                <span class="material-symbols-outlined">cannabis</span>
                <h3>Catnip</h3>
            </div>
            <div class="circle">
                <span class="material-symbols-outlined">pets</span>
                <h3>Righting Reflex</h3>
            </div>
            <div class="circle">
                <span class="material-symbols-outlined">sunny_snowing</span>
                <h3>Crepuscular Rhythm</h3>
            </div>
        </div>
    </section>

    <section class="container">
        <h2>Meow... an exclusive language</h2>
        <p>
            Adult cats almost never meow at each other. They use meowing
            exclusively to communicate with humans. Among themselves, they use
            body, scent and other sounds (like hissing or yowling, but the
            classic meow is to get youw attention)
        </p>

        <div class="grid">
            <div class="card">
                <h3>Acoustic manipulation</h3>
                <p>Some cats have perfected a special meo that mices a purr with high-pitched cry. This sound has a frequency similar to a human baby's cry, mahing it almost impossible for us to ignore (our brains are hardwire) to respond to that tone</p>
            </div>

            <div class="card">
                <h3>The silent Meow</h3>
                <p>
                    Sometimes a cat opens its mouth to meow but no sound comes out. Actually, they are meowing, but it is so high pitched that the human ear cannot hear it, although other animals might. It is also belived thay use it when they are exhausted or to show submission
                </p>
            </div>

            <div class="card">
                <h3>Unique Vocabulary</h3>
                <p>There is no universal cat language. Each cat developes its own set of meows and tones specific to their owner. The meow your cat uses to ask for food is different from the one another cat uses in a different house</p>
            </div>
        </div>

        
    </section>

    <section class="container" style="text-align: center; margin-bottom:8rem; margin-top:8rem;">
        <h2>Do you want to meet a <span id="Pink">Meow Meow?</span></h2>

        <a href="./play" class="btn">Play with a Meow Meow</a>
    </section>
</main>

<style>
    h1 {
        font-size: 4.2rem;
    }

    /* HERO CSS */
    .hero {
        background: linear-gradient(
                rgba(207, 161, 161, 0.35),
                rgba(49, 8, 8, 0.35)
            ),
            url("/images/background-hero.png");
        background-size: cover;
        background-position: center;
        height: 600px;
        text-align: center;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    .hero h1 {
        margin-bottom: 0;
    }
    .hero p {
        margin-bottom: 3rem;
    }

    /* INTRO SECTION */
    .intro {
        display: grid;
        grid-template-columns: 600px 1fr;
        align-items: center;
        margin-top: 5rem;
    }

    .intro img {
        margin: 0 auto 20px auto;
        max-width: 800px;
        border-radius: 10px;
    }

    /* INFO SECTION */
    .info {
        margin: 6rem 3rem;
    }

    .grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
        gap: 20px;
        margin-top: 3rem;
    }

    .mini-fact span {
        font-size: 6rem;
    }

    .mini-fact {
        background-color: rgb(226, 200, 209);
        border-radius: 50px;
        padding: 50px;
        text-align: center;
    }

    .mini-fact:hover {
        background-color: rgb(202, 174, 184);
        border-radius: 100px;
        padding: 50px;
        text-align: center;
    }

    /* Paragraph section */
    .whole-paragraph {
        margin-top: 10rem;
        text-align: center;
        background-color: rgb(218, 171, 181);
        padding: 100px 50px 200px 50px;
    }

    #Pink {
        color: rgb(196, 27, 97);
        text-decoration: underline;
        padding: 5px 25px;
    }

    #Pink:hover {
        background-color: rgb(194, 91, 134);
        border-radius: 20px;
        color: rgb(248, 182, 199);
        text-decoration: none;
    }

    #second {
        margin-top: -5.5rem;
        border-top: rgb(196, 27, 97) 2px dotted;
    }

    /* Other facts */

    #paragraphs {
        margin-top: -8rem;
    }

    .card {
        background-color: rgb(216, 121, 142);
        border-radius: 80px;
        padding: 40px;
        border: 3px rgb(139, 60, 77) solid;
    }

    .card:hover {
        background-color: rgb(238, 170, 170);
        border: 3px rgb(170, 88, 106) dotted;
    }

    /* Circles Section */
    .circles {
        margin: 10rem 0;
        text-align: center;
    }

    .circle {
        padding: 20px;
    }
    .circle span {
        background-color: rgb(224, 176, 201);
        padding: 60px;
        border-radius: 100px;
        font-size: 3rem;
        color: rgb(146, 10, 78);
    }

    .circle span:hover {
        background-color: rgb(196, 27, 97);
        color: rgb(255, 222, 238);
        padding: 70px;
        border-radius: 100px;
        font-size: 3.2rem;
    }

    /* MEDIA */

    @media (max-width: 999px) {
        .intro {
            flex-direction: column;
            grid-template-columns: 1fr;
            text-align: center;
        }
        .intro img {
            max-width: 400px;
        }
    }
    @media (max-width: 450px) {
        .intro img {
            max-width: 250px;
        }
    }
</style>
