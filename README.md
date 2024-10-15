A streaming library for Swarm that enables media streaming and playback (watching/listening) over Swarm.

# Watch

To play a stream, first initialize a player Bee node. This node handles all requests related to playing the stream. Then, you can use the VideoPlayer component.

```
import { playerBee, VideoPlayer } from 'swarm-stream-react-js';

playerBee.setBee('http://localhost:1633');
<VideoPlayer owner={"ETH address of the stream"} topic={"Topic name"} />
```

# Stream

To stream media, initialize a streamer Bee node. This node manages requests related to streaming.

```
import { isStreamOngoing, startStream, stopStream, streamBee } from 'swarm-stream-react-js';

streamBee.setBee('http://localhost:1633');

async function startStream(
  signer: Signer, // To continuously write to Swarm feeds, a Signer (public and private key pair) must be provided.
  topic: string, // A unique topic name. This is required for playing the stream via the VideoPlayer.
  stamp: BatchId, // A valid BatchId for writing to Swarm.
  options: Options // Configuration for the stream.
): Promise<void>;

interface Options {
  video: boolean; // Whether to include video in the stream
  audio: boolean; // Whether to include audio in the stream
  timeslice: number; // The interval at which to create media segments (currently fixed at 2000ms)
  videoBitsPerSecond: number; // Defines the video quality of the stream
}

function stopStream(): void;
function isStreamOngoing(): boolean;

```

# Limitations

Currently, the following browser features are required:

- 'video/webm; codecs=vp9,opus' (the library is specific to WebM format)
- Navigator.mediaDevices: MediaDevices API
- MediaRecorder: Media recording API
- MediaSource: Media source extensions
- SourceBuffer: Used for appending media segments

Tests have primarily been conducted using Chrome.

# How it works?

Streamer Side
------------
A new Swarm feed is created.
Each media segment from MediaRecorder is uploaded to the Swarm feed under a specific feed index.
The indexing is manual to avoid lookups.
Feed index 0 contains the stream's metadata, which is necessary to initialize the MediaSource.

Player Side
------------
The player first retrieves metadata from feed index 0.
It then waits for a cluster start (keyframe), ensuring that the cluster is clean.
The player starts appending subsequent segments in order once the cluster is established.

Helpful docs:
- https://docs.ethswarm.org/docs/develop/tools-and-features/feeds#what-are-feeds
- https://www.webmproject.org/
- https://en.wikipedia.org/wiki/Extensible_Binary_Meta_Language

An example demo project to demonstrate a simple use case:
https://github.com/Solar-Punk-Ltd/ethercast

