# Automated Jersey Number Recognition

This project will identify the jersey numbers of American football players in broadcast footage using a two stage approach. The first stage will be a pre-trained Mask R-CNN that will detect players, and the second stage will be a fine-tuned Faster R-CNN to extract digits from the player bounding boxes. This project was inherited from Alex Ramey, a former UA CS student. Much of the credit goes to him. Nguyen Nguyen, Jeff Reidy, and Noah Wagnon have added on to his existing project by adding roster integration, game clock information extraction, jersey color filtering, a linked database, 2-digit number synthesis, major speed boosts, and minor accuracy improvements

## Example Images

| | | |
|:----------:|:----------:|:----------:|
| ![][pic1]  | ![][pic2]  | ![][pic3]  |
| ![][pic4]  | ![][pic5]  | ![][pic6]  |
| ![][pic7]  | ![][pic8]  | ![][pic9]  |
| ![][pic10]  | ![][pic11] | ![][pic12] |

[pic1]: readme_assets/pic1.png
[pic2]: readme_assets/pic2.png
[pic3]: readme_assets/pic3.png
[pic4]: readme_assets/pic4.png
[pic5]: readme_assets/pic5.png
[pic6]: readme_assets/pic6.png
[pic7]: readme_assets/pic7.png
[pic8]: readme_assets/pic8.png
[pic9]: readme_assets/pic9.png
[pic10]: readme_assets/pic10.png
[pic11]: readme_assets/pic11.png
[pic12]: readme_assets/pic12.png

## Usage
Note: The "494-407 Project Demo.pdf" gives a step by step instruction for executing the system from end to end.

```bash
# From repository root directory
python main/main.py <INPUT_VIDEO_PATH>
```

## Football Dataset 
1. Capture frames from two Alabama football games (1280 x 720 resolution), one with home uniforms and the other with away uniforms.

1. Do a pass over the frames, removing ones that do not contain at least one Alabama player with a completely visible jersey number.

1. Run a [pre-trained Mask R-CNN](https://github.com/matterport/Mask_RCNN) to extract person bounding boxes from the frames.

1. Pad the bounding boxes with 0s (blackness) to create square images then re-scale to size 256 x 256. Discard non-Alabama players.

1. Label the digits with [VGG Image Annotator (VIA) tool](http://www.robots.ox.ac.uk/~vgg/software/via/).

For more details, see [data](data) folder, which holds its own README.md file.

## Training Approach

1. Start with the same [pre-trained Mask R-CNN](https://github.com/matterport/Mask_RCNN) as above. As shown in this [tutorial](https://machinelearningmastery.com/how-to-train-an-object-detection-model-with-keras/), it is possible to train the Mask R-CNN for object detection using a dataset that only contains bounding boxes.

1. Evaluate performance on `football_player_test` for the following three scenarios:
    
    1. Fine-tuning with [Street View House Numbers (SVHN)](http://ufldl.stanford.edu/housenumbers/) dataset
    1. Fine-tuning with `football_player_train`
    1. Fine-tuning with `SVHN + football_player_train` 

3. The best performing model is hereby referred to as `jersey_number_detector`.

## Other features

1. Synthesized the discrete digits into actual jersey numbers (i.e. '2' and '9' --> '29')
2. Filtered out away team jersey numbers based on color, which will vary per game
3. Supplemented the jersey numbers with roster information (player names)
4. Added game context using OCR on the scoreboard
5. Developed simple API to post play-by-play data to.

## Future Work
Utilize the DETR to perform more accurate person proposals in crowded regions.
