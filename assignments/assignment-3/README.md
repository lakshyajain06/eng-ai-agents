I used yolo26n as my base model and finetuned using this dataset:
https://universe.roboflow.com/dronenotdrone/drone-detection-pq8sj

This dataset contains two classes, one for drone, and one for bird. Making a guess, this is to train the model to not get confused between the two. When detecting for the drone, I exclude the bird class and only detect the drone class. I chose the confidence level by maximinzing the value on the F1 curve.

My kalman filter state vector composed of the center point of the bounding box, and the velocity (cx, cy, vx, vy). My transition matrix assumes a constant velocity between frames. I set a moderately high R, because I was not confident in my model predictions. High value for P because my inital prediction was not certain. Q is was set low because I thought it was a fair appproximation to assume constant velocity between my frames in a small timeframe.

My Kalman filter occasionaly fails due to my my model not fully catching all instances of the drone. Additionally there are a lot of frames where the drone is quite small and thus not muxh pixel indformation gets sent to the model for classification. For missed detections, I just cut off the tracking when my model fails to detect a constant number of times in a row. If it fails to detect for less, I allow the kalman filter to approximate the state..

My hugging face repo can be found here: https://huggingface.co/datasets/lakshyajain06/drone_detections

Sample Videos:
https://youtu.be/YsCF9pryw84
https://youtu.be/hqQgmU7Y_wI
