## Tuner with Long Tone Training Game

This is a tuner designed with wind instrumentalists in mind. 
A key part of wind technique is to generate a musical pitch in tune at various dynamic levels.
To master this, wind players must practice long tones, literally just playing long notes at various volumes to practice being in tune.
This is a tedious and boring task, and it is the goal of the this tuner to game-ify the process.
The tuner uses the P5 sound and play libraries.
It receives input from the Microphone, uses FFT to analyze frequency spectrums and an autocorrelation algorithm to detect fundamental frequency, and cents.
It also uses the source level to determine volume.
To average the cents and volume inputs, a counter is used and with modulus 30, the inputs are averaged.
The training game is similar to Flappy Bird.
It uses sprites to generate a player sprite and obstacle pipes.
The player sprite moves along the y-axis according to volume level.
By being within a cent range of -5 to 5, and successfully passing pipes, users will score points, thus training intonation at various dynamic levels as pipes spawn at random heights.


## Documentation

The program first identifies the source of sound as the device's microphone. Using the fft function, it gathers all frequencies picked up b the microphone. This function is from the sound library.

To find the fundamental frequency of the sound being picked up, it uses the autocorrelation algorithm, based on code by [therewasaguy](https://github.com/therewasaguy/p5-music-viz)

The fundamental frequency is then converted to a numerical representation of a note through the following function:  
function noteFromPitch(freq) {
    var noteNum = 12 * (Math.log(freq / 440) / Math.log(2));
    return Math.round(noteNum) + 69;}
    
This is then converted to a letter by taking the modulo 12 and mapping it to an array containing the 12 different note names.

In order to find cents off from center of the pitch, we use the following function to find the in tune frequency based on the note being played:
function frequencyFromNoteNumber(note) {
    return 440 * Math.pow(2, (note - 69) / 12);
} with note being the determined note from the fundamental frequency.

Then we use this ideal frequency and compare it to the actual played frequency to determine cents:
function centsOffFromPitch(freq, note) {
    return Math.floor(1200 * Math.log(freq / frequencyFromNoteNumber(note)) / Math.log(2));
}

volume is also determined using the source.getLevel() function from the P5 sound library.

In order to get a more accurate cents and volume reading the following code is used:
var counter = 0;
var cents = 0;
var centsTot = 0;
var volume = 0;
var volumeTot = 0;

all above the setup. 

in the draw loop:

counter++

 volumeTot += source.getLevel();
    if (counter % 30 == 0) {
        volume = volumeTot / 30;
        console.log(volume.toFixed(2));
        volumeTot = 0;
    }
(for volume)
     
and

 centsTot += centsOffFromPitch(freq, note);
        if (counter % 30 == 0) {
        cents = centsTot / 30;
        centsTot = 0;
    }   
(for cents)

The auto correlation function and Game mode are both initialized by having a volume > 0.00.

The game/ long tone training mode was created using the P5 play library.

These variables are used before the setup:
var MAX_OPENING;
var bird, ground;
var pipes;
var gameOver;

The setup is used to make the player sprite (bird):
 bird = createSprite(width, height / 2, 40, 40);
bird.rotateToDirection = true;
bird.setCollider("circle", 0, 0, 20);

and the obstacle group, plus initial conditions:

pipes = new Group();
    gameOver = true;
    updateSprites(false);
    
The vertical position of the player sprite is mapped through volume:
var y1 = map(volume.toFixed(2), 0.00, 0.35, height, 0);
bird.position.y = y1;

The player dies when it hits a pipe obstacle:
if (bird.overlap(pipes))
            die();
            
which triggers the die function:
function die() {
    updateSprites(false);
    gameOver = true;
}

Pipes spawn based on frameCount and modulo 180.
and the distance of the opening between them is determined by the height of the canvas - the randomized height of the bottom pipe.

//spawn pipes
        if (frameCount % 180 == 0) {
            var pipeH = random(50, 1000);
            var pipe = createSprite(bird.position.x + width, height, 80, pipeH);
            pipe.velocity.x = -4;
            pipes.add(pipe);
            //top pipe
            if (pipeH > 0) {
                MAX_OPENING = height - pipeH - 50;
                var pipe2 = createSprite(bird.position.x + width, 0, 80, height - pipeH);
                pipe2.velocity.x = -4;
                pipes.add(pipe2);
            }
        }
        
The pipes move across the screen, giving the illusion of player movement, while allowing the text to stay on screen.

Scoring works by being withing a certain cent range and successfully passing pipes:

// scoring
        for (var i = 0; i < pipes.length; i++) {
            if (pipes[i].position.x < bird.position.x && cents >= -5 && cents <= 5) {
                score++;
            }
            if (score > hiScore) {
                hiScore = score;
            }
        }

When the player dies everything is reset except for high score, which remains the same until the score surpasses it.

Text in the upper left hand corner displays note name, cents, score, high score, and instructions for play.

View the code [here](https://github.com/Robbie219/CIM540/blob/master/Final/empty-example/sketch.js)

Try the code [here](https://robbie219.github.io/)

-Robbie Harter
        
        






     
     
    



