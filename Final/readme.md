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