

### transmit gstreamer pipeline from laptop usb camera

	gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, format=I420, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=192.168.0.11 port=9500 sync=false async=false

### transmit gstreamer pipeline for intel compute stick with Microsoft LifeCam 720p usb cam

	gst-launch-1.0 v4l2src device=/dev/video0 ! videoconvert ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=192.168.43.46 port=9500 sync=false async=false

### receive gstreamer pipeline for camera over local wifi router

	gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=480, width=640 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! xvimagesink

### another receive gstreamer pipeline for camera over local wifi router

	gst-launch-1.0 udpsrc port=9500 ! 'application/x-rtp, encoding-name=H264, payload=96, height=(int)1920, width=(int)1080, framerate=(fraction)30/1' ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! autovideosink

### receive gstreamer pipeline for laptop receiving over local wifi router (or mobile hotspot) from LifeCam camera

	gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=720, width=1280 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! xvimagesink

### Save webcam video to webm/vp8 file format 

	gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! webmmux ! filesink location=test_rob.webm

### Stream webcam video to display with vp8 encoding and decoding

	gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! rtpvp8pay ! queue ! application/x-rtp,media=video,encoding-name=VP8,payload=97 ! decodebin ! queue ! videoconvert ! xvimagesink

### Example illustrating audio and video saved to file with one command (Note: save speed much faster than playback speed.)

	gst-launch-1.0 audiotestsrc ! vorbisenc ! oggmux name=mux !   filesink location=file.ogg videotestsrc pattern=ball ! theoraenc ! mux.

### Save a single frame of video to jpg file

	gst-launch-1.0 videotestsrc num-buffers=1 ! jpegenc !   filesink location=videotestsrc-frame.jpg

### Recording audio and video from webcam

	gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! webmmux name=mux ! filesink location=test_rob.webm  pulsesrc ! audioconvert ! vorbisenc ! mux.

### Play youtube video (with audio)

	T_DEBUG=3 gst-launch-1.0 souphttpsrc is-live=true location="$(youtube-dl --format "best[ext=mp4][protocol=https]" --get-url https://www.youtube.com/watch?v=ndl1W4ltcmg)" ! qtdemux name=demuxer  demuxer. ! queue ! avdec_h264 ! autovideosink  demuxer.audio_0 ! avdec_aac ! autoaudiosink
	
### Save youtube video to file

	gst-launch-1.0 souphttpsrc is-live=true location="$(youtube-dl --format "best[ext=mp4][protocol=https]" --get-url https://www.youtube.com/watch?v=ndl1W4ltcmg)" ! filesink location=video.mp4 -e


### filesrc (compressed video) to shmsink

	st-launch-1.0 filesrc location =../RobWebCam_recordings/video_only2.mp4 ! shmsink socket-path=/tmp/webrtc sync=true wait-for-connection=false

### shmsrc (compressed video) to autovideosink

	st-launch-1.0 shmsrc socket-path=/tmp/webrtc ! decodebin ! queue ! videoconvert ! autovideosink 

OR (with Nvidia's videoconvert)

	gst-launch-1.0 shmsrc socket-path=/tmp/webrtc ! decodebin ! queue ! nvvideoconvert ! autovideosink 

### filesrc (uncompressed video) to shmsink

	gst-launch-1.0 filesrc location =../RobWebCam_recordings/video_only2.mp4 ! decodebin !  shmsink socket-path=/tmp/webrtc sync=true wait-for-connection=false

	gst-launch-1.0 filesrc location =../RobWebCam_recordings/video_only2.mp4 ! decodebin ! video/x-raw, format=(string)RGB, width=(int)640, height=(int)480, framerate=(fraction)30/1 ! shmsink socket-path=/tmp/webrtc sync=true wait-for-connection=false


### shmsrc (uncompressed video) to autovideosink

	gst-launch-1.0 shmsrc socket-path=/tmp/webrtc !  "video/x-raw, format=(string)YUY2, width=(int)640, height=(int)480, framerate=(fraction)30/1" ! autovideosink


### v4l2src to udpsink

	gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=640, height=480' ! videoconvert ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=127.0.0.1 port=9500 sync=false async=false 

OR (to Digital Ocean droplet for video forwarding)

	gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=1920, height=1080' ! videoconvert ! x264enc tune=zerolatency ! rtph264pay config-interval=1 pt=96 ! udpsink host=$DO_IP_ADDRESS port=$DO_OUTPORT sync=false async=false

### v4l2src to shmsink

	gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=640, height=480' ! videoconvert ! x264enc tune=zerolatency ! shmsink socket-path=/tmp/webrtc sync=true wait-for-connection=false 

### updsrc to autovideosink

	gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=480, width=640 ! rtph264depay ! h264parse ! avdec_h264 ! videoconvert ! autovideosink sync=false async=false
 
OR 
 
	gst-launch-1.0 udpsrc port=9500 ! application/x-rtp, encoding-name=H264, payload=96, height=480, width=640 ! rtpjitterbuffer ! rtph264depay ! decodebin ! queue ! videoconvert ! autovideosink sync=false async=false

### filesrc to udpsink

	gst-launch-1.0 filesrc location=Videos/RobWebCam_recordings/video_only2.mp4 ! decodebin ! x264enc tune=zerolatency ! rtph264pay ! udpsink host=127.0.0.1 port=9500 sync=false async=false 
	
or, to avoid having to decode and re-encode, a better way is:
	
	gst-launch-1.0 filesrc location=Videos/RobWebCam_recordings/video_only2.mp4 ! qtdemux ! video/x-h264 ! rtph264pay config-interval=1 pt=96 ! udpsink host=127.0.0.1 port=9500


### videotestsrc (uncompressed video) to udpsink

	gst-launch-1.0 videotestsrc ! rtpvrawpay ! udpsink host=127.0.0.1 port=9500 sync=false async=false 

### udpsrc (uncompressed video) to autovideosink

	gst-launch-1.0 udpsrc port=9500 ! "application/x-rtp, clock-rate=(int)90000,  media=(string)video, encoding-name=(string)RAW, payload=(int)96, sampling=(string)YCbCr-4:2:0, depth=(string)8, height=(string)240, width=(string)320" ! rtpvrawdepay ! autovideosink sync=false


### v4l2src and pulsesrc to filesink

	gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480 ! videoconvert ! queue ! vp8enc deadline=1 ! webmmux name=mux ! filesink location=test_rob.webm pulsesrc ! audioconvert ! vorbisenc ! mux.

### udpsrc to filesink

	gst-launch-1.0 udpsrc port=9500 ! "application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264, payload=(int)96, height=(int)480, width=(int)640" ! rtph264depay ! h264parse ! mp4mux ! filesink location=test.mp4


### filesrc to rtspclientsink (must run rtsp-simple-server from https://github.com/aler9/rtsp-simple-server)

	gst-launch-1.0 filesrc location=file.mp4 ! qtdemux ! rtspclientsink location=rtsp://localhost:8554/mystream
	

### rtspsrc to autovideosink

for above stream publish command:

	gst-launch-1.0 rtspsrc location=rtsp://localhost:8554/mystream latency=200 ! decodebin ! queue ! videoconvert ! autovideosink sync=false
	
or for DO server:

	gst-launch-1.0 rtspsrc location=rtsp://$DO_IP_ADDRESS:$DO_INPORT/test latency=200 ! decodebin ! queue ! videoconvert ! autovideosink sync=false

### udpsrc to rtspclientsink (using rtsp-simple-server from https://github.com/aler9/rtsp-simple-server)

	gst-launch-1.0 udpsrc port=9500 ! "application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264, payload=(int)96, height=(int)480, width=(int)640" ! rtph264depay ! rtspclientsink location=rtsp://localhost:8554/mystream




