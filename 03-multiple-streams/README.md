# multiple streams

Usage

`./deepstream-test3-app file:///opt/nvidia/deepstream/deepstream-5.0/samples/streams/sample_1080p_h264.mp4 file:///opt/nvidia/deepstream/deepstream-5.0/samples/streams/sample_720p.mp4`

You can also use a rtsp uri as input, the file can be h264/h265/elementary stream

This sample builds on top of the deepstream-test1 sample to demonstrate how to:

* Use multiple sources in the pipeline.
* Use a uridecodebin so that any type of input (e.g. RTSP/File), any GStreamer
  supported container format, and any codec can be used as input.
* Configure the stream-muxer to generate a batch of frames and infer on the
  batch for better resource utilization.
* Extract the stream metadata, which contains useful information about the
  frames in the batched buffer.
