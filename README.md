<p align="center" >
  <img src="https://jiomeetpro.jio.com/assets/img/website/website_logo_header_light.svg" title="logo" float=center  height="150">
</p>

# 👋 JioMeet Hooks Web SDK

Step into the future of React development with our groundbreaking Jio Meet Hooks SDK. It seamlessly blends the simplicity of React's declarative syntax with powerful audio, video, and screenShare capabilities. Elevate your projects, create dynamic interfaces, and redefine user engagement. Welcome to a new era of development—where your React applications become truly extraordinary with the magic of our Hooks SDK 🚀!

## Table of Contents

- [👋 Introduction](#-jioMeet-web-core-template-sdk)
- [🚀 How to Install SDK](#-how-to-install-sdk)
- [🏁 Setup](#-setup)
- [🌟 Provider](#-provider)
- [🎨 Component](#-component)
- [🪝 Hooks](#-hooks)

## 🚀 How to Install SDK

You can install the SDK using npm :

To install via [NPM](https://www.npmjs.com/):

```bash
npm install @jiomeet/hooks-sdk-web
```

## 🏁 Setup

#### Register on JioMeet Platform:

You need to first register on Jiomeet platform. [Click here to sign up](https://platform.jiomeet.com/login/signUp)

#### Get your application keys:

Create a new app. Please follow the steps provided in the [Documentation guide](https://dev.jiomeet.com/docs/quick-start/introduction) to create apps before you proceed.

#### Get your Jiomeet meeting id and pin

Use the [create meeting api](https://dev.jiomeet.com/docs/JioMeet%20Platform%20Server%20APIs/create-a-dynamic-meeting) to get your room id and password

## 🌟 Provider

### 🔓 JMRoomProvider

You need to wrap your top most component with these provider after that you can access any hook from the SDK

##### Example:

```typescript
import { JMRoomProvider } from '@jiomeet/hooks-sdk-web';

const Client = ({ children }) => {
	return <JMRoomProvider>{children}</JMRoomProvider>;
};
const root = createRoot(document.getElementById('container'));
root.render(<Client />);
```

## 🪝 Hooks

### 🤝 useJoin

This hook simplifies the process of joining meetings in your React application.

```typescript
useJoin {
  joinMeeting: (meetingData: IJMJoinMeetingParams) => Promise<void>;
  publishLocalPeerConfig: (config: IJMMediaSetting) => Promise<void>;
  isCallStarted: boolean;
}
```

##### Returns:

- **joinMeeting** :<code>joinMeeting(meetingData: <a href="#ijmjoinmeetingparams">IJMJoinMeetingParams</a>): Promise&lt;string&gt;</code>
  This function is used to join the call/Room
- **publishLocalPeerConfig** : <code>publish(config: <a href="#ijmmediasetting">IJMMediaSetting</a>): Promise&lt;string&gt;</code>
  After successfully joining the call , you can use this method to publish the initial audio , video, It allows you to publish audio and video tracks at a same time. This can be particularly useful when users select audio and video settings on a preview screen, or when you want to join a call with audio and video unmuted, or alternatively you can use audioUnmute and videoUnmute individually
- **isCallStarted** : optional `boolean`
  Weather you join the call or not , once you successfully join the call it give `True` otherwise `False`

##### Example:

```typescript
import { useJoin } from '@jiomeet/hooks-sdk-web';

function App() {
	const { joinMeeting, publishLocalPeerConfig, isCallStarted } = useJoin();
	const handleJoin = async () => {
		await joinMeeting({
			meetingId: '12345678',
			meetingPin: 'pqrst',
			userDisplayName: 'Rahul',
			config: { userRole: 'speaker' },
		});
	};

	return (
		<div>
			{isCallStarted ? 'Join Success' : 'Not Join Yet'}
			<div>
				<button onClick={() => handleJoin()}>Join</button>
			</div>
		</div>
	);
}
```

### 👥 useRemotePeers

This hook give details of remote peers available in the call

```typescript
useRemotePeers {
  remotePeers: IJMRemotePeer[];
  getRemotePeerById: (id: string ) => IJMRemotePeer | null | undefined;
  isPeerDominantSpeaker: (id: string) => boolean;
}
```

##### Returns:

- **remotePeers** :[`IJMRemotePeer[]`](#ijmremotepeer)
  This will return array of object [`IJMRemotePeer`](#ijmremotepeer) of all the peer available in the room
- **getRemotePeerById** : <code>publish(id: string): Promise&lt;IJMRemotePeer | null | undefined;&gt;</code>
  This function give the specific remote peer info by taking peerId as a parameter
- **isPeerDominantSpeaker** : : <code>isPeerDominantSpeaker(id: string): boolean</code>
  Weather this particular user is currently top speaker or not

##### Example:

```typescript
import { useRemotePeers } from '@jiomeet/hooks-sdk-web';

function App() {
  const { remotePeers, getRemotePeerById, isPeerDominantSpeaker } =
    useRemotePeers();

  return (
    <div>
      {remotePeers.map(remotePeer => {
        return (
            <div>{`${remotePeer.name}`} </div>
            <div>mic: {remotePeer.audioMuted ? 'muted' : 'unmuted'}</div>
            <div> video: {remotePeer.videoMuted ? 'muted' : 'unmuted'}</div>
        )
      })}
    </div>
  );
}
```

### 🤝 useVideo

This hook is used to render a remote video in div , and it take care of subscription and unSubscription of video to save bandwidth it only subscribe when video is render and auto unSubscribe when parent div remove from the DOM

<p style="padding: 10px; background-color: rgba(255,229,100,.3); border-radius:5px"><span style="font-weight:bold">Important:</span> Note: 
Audio is played by SDK , whenever remote user unmute it automatically subscribe and start playing


```typescript
useVideo {
  containerRef: RefCallback<HTMLDivElement>;
}
```

##### Parameters:

- **peerId** : `string`
  Remote Peer Id whose video/screen share you want to render
- **mediaType** :[`IJMMediaType`](#ijmmediatype)
  Media Type which you want to render. video or screenShare it will subscribe and render if remote user has enabled there video,screenShare
- **subscribe** : optional `boolean`
  Weather you want to subscribe that media or not ,default is `True`
- **config** :[`IJMVideoPlayerConfig`](#ijmvideoplayerconfig)
  Additional config to render that media like `content-fit` and `mirror` property

##### Returns:

- **containerRef** : `RefCallback<HTMLDivElement>`
  This hook return the container reference which you can give ti your div where you want to render the media

##### Example:

```typescript
import { useVideo } from '@jiomeet/hooks-sdk-web';

function App(peer:IJMRemotePeer) {
 const { containerRef } = useVideo({
    "xyz",
    "video",
     true,
    {
      fit: 'contain',
      mirror: false,
    },
  });

  return (
     {!peer.videoMuted && (
     <div ref={containerRef}></div>
      )}
  );
}
```

### 👤 useLocalPeer

This hook give details of local peer and

```typescript
useLocalPeer {
  localPeer: IJMLocalPeer;
  playVideoTrack(trackId: string, element: string | HTMLElement): Promise<void>;
  leaveMeeting: () => Promise<void>;
}
```

##### Returns:

- **localPeer** :[`IJMLocalPeer`](#ijmlocalpeer)
  This will return of object [`IJMLocalPeer`](#ijmlocalpeer) which have all the details of local peer
- **playVideoTrack** : <code>playVideoTrack(trackId: string,element: string | HTMLElement): Promise&lt;void&gt;</code>
  This function use to play local video track , If you are using template then it will take care internally
- **leaveMeeting** : <code>leaveMeeting(): Promise&lt;void&gt;</code>
  This function is use to leave the meeting/Room

##### Example:

```typescript
import { useLocalPeer } from '@jiomeet/hooks-sdk-web';

function App() {
	const { leaveMeeting } = useLocalPeer();

	const handleLeave = async () => {
		await leaveMeeting();
	};

	return (
		<div>
			<button onClick={() => handleLeave()}>Join</button>
		</div>
	);
}
```

### 📽 useAVToggle

This hook give functionality to mute and unmute audio , video of local peer

```typescript
useAVToggle {
  isLocalAudioMuted: boolean;
  isLocalVideoMuted: boolean;
  toggleLocalAudio: () => Promise<void>;
  toggleLocalVideo: () => Promise<void>;
}
```

##### Returns:

- **isLocalAudioMuted** :`Boolean`
  This will return the current audio mute state of local peer , If Audio mute it return `True` other wise false
- **isLocalVideoMuted** :`Boolean`
  This will return the current video mute state of local peer , If Video mute it return `True` other wise false
- **toggleLocalAudio** : <code>toggleLocalAudio(): Promise&lt;void&gt;</code>
  This function use to toggle audio of local peer
- **toggleLocalVideo** : <code>toggleLocalVideo(): Promise&lt;void&gt;</code>
  This function use to toggle video of local peer

##### Example:

```typescript
import { useAVToggle } from '@jiomeet/hooks-sdk-web';

function App() {
	const { isLocalAudioMuted, toggleLocalAudio } = useAVToggle();

	const toggleMic = async () => {
		await toggleLocalAudio();
	};

	return (
		<div>
			<h1>{isLocalAudioMuted ? 'Your audio muted' : 'Your audio unmuted'} </h1>
			<button onClick={() => toggleMic()}>Join</button>
		</div>
	);
}
```

### 📽 useScreenShare

This hook give functionality to mute and mute audio , video of local peer

```typescript
useScreenShare {
  isLocalScreenShared: boolean;
  toggleScreenShare: () => Promise<void>;
  screenSharePeerId?: string | undefined;
}
```

##### Returns:

- **isLocalScreenShared** :`Boolean`
  This will return the current screen state of local peer , If Screen is shared by local peer it return `True` other wise `False`
- **toggleScreenShare** : <code>toggleScreenShare(): Promise&lt;void&gt;</code>
  This function use to toggle screen share of local peer
- **screenSharePeerId** : `String` | `undefined`
  This will return id of the peer who is currently sharing screen, will only be present if there is a screenshare in the room

##### Example:

```typescript
import { useScreenShare } from '@jiomeet/hooks-sdk-web';

function App() {
	const { toggleScreenShare, isLocalScreenShared } = useScreenShare();

	const toggleMyScreenShare = async () => {
		await toggleScreenShare();
	};

	return (
		<div>
			<h1>{isLocalScreenShared ? 'Your are sharing screen' : 'Your are not sharing screen'}</h1>
			<button onClick={() => toggleMyScreenShare()}>Join</button>
		</div>
	);
}
```


### ⚙️ useDevices

This Custom hook that provides access to audio and video devices and allows to change the the current audio/video device.

<p style="padding: 10px; background-color: rgba(255,229,100,.3); border-radius:5px"><span style="font-weight:bold">Important:</span> Note: Browsers give access to the list of devices only if the user has given permission to access them

```typescript
useDevices {
  getMediaPermissions: () => Promise<void>;
  allDevices: IJMDeviceMap;
  audioInputDevices: MediaDeviceInfo[];
  audioOutputDevices: MediaDeviceInfo[];
  videoInputDevices: MediaDeviceInfo[];
  setAudioInputDevice: (deviceId: string) => Promise<void>;
  setAudioOutputDevice: (deviceId: string) => Promise<void>;
  setVideoDevice: (deviceId: string) => Promise<void>;
  currentAudioInputDeviceId: string | undefined;
  currentAudioOutDeviceId: string | undefined;
  currentVideoInputDeviceId: string | undefined;
}
```

##### Returns:

- **getMediaPermissions** : <code>getMediaPermissions(): Promise&lt;void&gt;</code>
  This function is used to get microphone and camera permission
- **allDevices** : [`IJMDeviceMap`](#ijmdevicemap)
  It will return the object of type [`IJMDeviceMap`](#ijmdevicemap) which have array of audioInput,audioOutput,videoInput devices which consist webrtc [`MediaDeviceInfo`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
- **audioInputDevices** : [`MediaDeviceInfo[]`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
  It will return the list of all audioInput/Microphone devices available as a array of object type [`MediaDeviceInfo`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
- **audioOutputDevices** : [`MediaDeviceInfo[]`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
  It will return the list of all audioOutput/Speaker devices available as a array of object type [`MediaDeviceInfo`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
- **videoInputDevices** : [`MediaDeviceInfo[]`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
  It will return the list of all videoInput/Camera devices available as a array of object type [`MediaDeviceInfo`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo)
- **setAudioInputDevice** : <code>setAudioInputDevice(deviceId:string): Promise&lt;void&gt;</code>
  It is a function which use to set audioInput/Microphone device in the room it take `deviceId` as a parameter
- **setAudioOutputDevice** : <code>setAudioOutputDevice(deviceId:string): Promise&lt;void&gt;</code>
  It is a function which use to set audioOutput/Speaker device in the room it take `deviceId` as a parameter
- **setVideoDevice** : <code>setVideoDevice(deviceId:string): Promise&lt;void&gt;</code>
  It is a function which use to set videoInput/Camera device in the room it take `deviceId` as a parameter
- **currentAudioInputDeviceId** : `string` | `undefined`
  It will return the current audioInput/Microphone devices used
- **currentAudioOutDeviceId** : `string` | `undefined`
  It will return the current audioOutput/Speaker devices used
- **currentVideoInputDeviceId** : `string` | `undefined`
  It will return the current videoInput/Camera devices used

##### Example:

```typescript
import { useDevices } from '@jiomeet/hooks-sdk-web';

function App() {
	const { currentAudioInputDeviceId, setAudioInputDevice } = useDevices();
	const optionChange = async (deviceId: string) => {
		await setAudioInputDevice(deviceId);
	};

	return (
		<div>
			{audioInputDevices.map((device) => (
				<div key={device.deviceId} onClick={() => optionChange(device)}>
					{device.label}
				</div>
			))}
		</div>
	);
}
```

### 👤 useHostControls

This hook gives host control methods

```typescript
  useHostControls {
  toggleForceMute?: () => Promise<void>;
  softMute?:()=>Promise<void>;
  endMeeting?: () => Promise<void>;
  removePeerFromRoom?: (peerId: string) => Promise<void>;
  lowerRemotePeerHand: (peerId: string) => Promise<void>;
  toggleMeetingLock?: () => Promise<void>;
  isMeetingLocked: boolean;
  isHost: boolean;
  isRoomAudioForceMute: boolean;
}
```

##### Returns:

- **toggleForceMute** : <code>toggleForceMute(): Promise&lt;void&gt;</code>
  This function is use to toggle the force mute on the room. Participants will not be able to turn on their mic if room is on force mute.
- **softMute** : <code>softMute(): Promise&lt;void&gt;</code>
  This function is use to mute all the participants of the meeting
- **endMeeting** : <code>endMeeting(): Promise&lt;void&gt;</code>
  This function is use to end the meeting/Room for all
- **removePeerFromRoom** : <code>removePeerFromRoom(peerId:string): Promise&lt;void&gt;</code>
  This function is use to remove a specific peer from room/meeting
- **lowerRemotePeerHand** : <code>lowerRemotePeerHand(peerId:string): Promise&lt;void&gt;</code>
  This function is use to lower the hand of remote peer
- **toggleMeetingLock** : <code>toggleMeetingLock(): Promise&lt;void&gt;</code>
  This function is use to toggle the lock on the meeting
- **lowerRemotePeerHand** : <code>lowerRemotePeerHand(peerId:string): Promise&lt;void&gt;</code>
  This function is use to lower the hand of remote peer
- **isMeetingLocked** : optional `boolean`
  Weather the meeting is locked or unlocked ,if the meeting is locked it give `True` otherwise `False`
- **isHost** : `boolean`
  Weather the local user is host or not, if local user is host then returns `True` otherwise `False`

##### Example:

```typescript
import { useHostControls, useRemotePeers } from '@jiomeet/hooks-sdk-web';

function App() {
	const { endMeeting, removePeerFromRoom, isHost } = useHostControls();
	const { remotePeers } = useRemotePeers();

	const handleEndMeeting = async () => {
		if (!endMeeting) return;
		await endMeeting();
	};

	const handleRemovePeer = async (id: string) => {
		if (!removePeerFromRoom) return;
		await removePeerFromRoom(id);
	};

	return (
		<div>
			<button onClick={() => handleEndMeeting()}>End Meeting</button>
			{remotePeers.map((remotePeer) => (
				<div key={remotePeer.peerId}>
					<div>{`${remotePeer.name}`}</div>
					<div>mic: {remotePeer.audioMuted ? 'muted' : 'unmuted'}</div>
					<div>video: {remotePeer.videoMuted ? 'muted' : 'unmuted'}</div>
					{isHost && <button onClick={() => handleRemovePeer(remotePeer.peerId)}>Remove Peer</button>}
				</div>
			))}
		</div>
	);
}
```

### 👤 useJMEvents

This hook allows you to listen to different events from meeting

##### Example:

```typescript
import { useJMEvents } from '@jiomeet/hooks-sdk-web';
const dominantSpeakerEvent = useJMEvents('DOMINANT_SPEAKER');
useEffect(() => {
	if (dominantSpeakerEvent.data.remotePeer) {
		console.log("current dominant speaker",remotePeer.name)
	}
}, [dominantSpeakerEvent]);
```

## 📖 Interfaces

#### `IJMJoinMeetingParams`

```typescript
interface IJMJoinMeetingParams {
	meetingId: string;
	meetingPin: string;
	userDisplayName: string;
	config: {
		userRole: 'host' | 'audience' | 'speaker';
		token?: string;
	};
}
```

#### `IJMRemotePeer`

```typescript
interface IJMRemotePeer {
	peerId: string;
	name: string;
	joinedAt?: string;
	metadata?: string;
	audioMuted: boolean;
	videoMuted: boolean;
	screenShare: boolean;
	isHost: boolean;
	audioTrack?: IJMRemoteAudioTrack;
	videoTrack?: IJMRemoteVideoTrack;
	screenShareTrack?: IJMRemoteScreenShareTrack;
}
```

#### `IJMLocalPeer`

```typescript
interface IJMLocalPeer {
	peerId: string;
	name: string;
	joinedAt?: string;
	metadata?: string;
	audioMuted: boolean;
	videoMuted: boolean;
	screenShare: boolean;
	isHost: boolean;
	audioTrack?: IJMLocalAudioTrack;
	videoTrack?: IJMLocalVideoTrack;
	screenShareTrack?: IJMLocalScreenShareTrack;
}
```

```typescript
interface IJMMediaSetting {
	trackSettings: {
		audioMuted: boolean;
		videoMuted: boolean;
		audioInputDeviceId?: string;
		audioOutputDeviceId?: string;
		videoDeviceId?: string;
	};
	virtualBackgroundSettings: {
		isVirtualBackground: boolean;
		sourceType: 'blur' | 'image' | 'none';
		sourceValue: string;
	};
}
```

#### `IJMConferenceConfigurations`

```typescript
export interface IJMConferenceConfigurations {
	configurations: {
		hideCallControls?: boolean;
		disableAudioOnlyMode?: boolean;
		defaultMediaSettings?: IJMMediaSetting;
		disableToasts?: boolean;
	};
}
```

#### `IJMDeviceMap`

```typescript
interface IJMDeviceMap {
	audioInput: MediaDeviceInfo[];
	audioOutput: MediaDeviceInfo[];
	videoInput: MediaDeviceInfo[];
}
```

#### `IJMVideoPlayerConfig`

```typescript
 interface IJMVideoPlayerConfig {
  mirror?: boolean;
  fit?: 'cover' | 'contain' | 'fill';
}
```
