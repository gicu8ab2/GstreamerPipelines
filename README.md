

## transmit gstreamer pipeline from laptop usb camera
  gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, format=I420, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=192.168.0.11 port=9500 sync=false async=false

## transmit gstreamer pipeline for intel compute stick with Microsoft LifeCam 720p usb cam
  gst-launch-1.0 v4l2src device=/dev/video0 ! videoconvert ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=192.168.43.46 port=9500 sync=false async=false

## receive gstreamer pipeline for camera over local wifi router
  gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=480, width=640 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! xvimagesink

## another receive gstreamer pipeline for camera over local wifi router
  gst-launch-1.0 udpsrc port=9500 ! 'application/x-rtp, encoding-name=H264, payload=96, height=(int)1920, width=(int)1080, framerate=(fraction)30/1' ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! autovideosink

## receive gstreamer pipeline for laptop receiving over local wifi router (or mobile hotspot) from LifeCam camera
  gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=720, width=1280 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! xvimagesink

## receive pipepline for reading video from Digital Ocean server
   gst-launch-1.0 rtspsrc location=rtsp://104.248.4.24:554/test latency=0 ! decodebin ! videoconvert ! xvimagesink

## Save webcam video to webm/vp8 file format
   gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! webmmux ! filesink location=test_rob.webm

## Stream webcam video to display with vp8 encoding and decoding
   gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! rtpvp8pay ! queue ! application/x-rtp,media=video,encoding-name=VP8,payload=97 ! decodebin ! queue ! videoconvert ! xvimagesink

## Example illustrating audio and video saved to file with one command (Note: save speed much faster than playback speed.)
   gst-launch-1.0 audiotestsrc ! vorbisenc ! oggmux name=mux !   filesink location=file.ogg videotestsrc pattern=ball ! theoraenc ! mux.

## Save a single frame of video to jpg file
   gst-launch-1.0 videotestsrc num-buffers=1 ! jpegenc !   filesink location=videotestsrc-frame.jpg