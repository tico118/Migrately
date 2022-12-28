import React, { useEffect, useRef, useCallback, useState } from "react";
import {
  useLocalParticipant,
  useVideoTrack,
  useDevices,
  useDaily,
  useDailyEvent,
  DailyVideo,
} from "@daily-co/daily-react";
import PropTypes from "prop-types";
import VideochatUserMediaError from "./VideochatUserMediaError";
import VideochatHeader from "./VideochatHeader";
import "../videochat/videochatstyles.css";

export default function VideochatHairCheck(props) {
  const joinCall = props.joinCall;
  const cancelCall = props.cancelCall;

  const localParticipant = useLocalParticipant();
  const videoTrack = useVideoTrack(localParticipant?.session_id);
  const {
    microphones,
    speakers,
    cameras,
    setMicrophone,
    setCamera,
    setSpeaker,
  } = useDevices();
  const callObject = useDaily();
  const videoElement = useRef();

  const [getUserMediaError, setGetUserMediaError] = useState(false);

  useDailyEvent(
    "camera-error",
    useCallback(() => {
      setGetUserMediaError(true);
    }, [])
  );

  const onChange = (e) => {
    callObject.setUserName(e.target.value);
  };

  const join = (e) => {
    e.preventDefault();
    joinCall();
  };

  useEffect(() => {
    if (!videoTrack.persistentTrack) return;
    if (videoElement?.current) {
      videoElement.current.srcObject =
        videoTrack.persistentTrack &&
        new MediaStream([videoTrack?.persistentTrack]);
    }
  }, [videoTrack.persistentTrack]);

  const updateMicrophone = (e) => {
    setMicrophone(e.target.value);
  };

  const updateSpeakers = (e) => {
    setSpeaker(e.target.value);
  };

  const updateCamera = (e) => {
    setCamera(e.target.value);
  };

  const microphonesMapper = (mic) => {
    return (
      <option key={`mic-${mic.device.deviceId}`} value={mic.device.deviceId}>
        {mic.device.label}
      </option>
    );
  };

  const speakersMapper = (speaker) => {
    return (
      <option
        key={`speaker-${speaker.device.deviceId}`}
        value={speaker.device.deviceId}
      >
        {speaker.device.label}
      </option>
    );
  };

  const camerasMapper = (camera) => {
    return (
      <option
        key={`cam-${camera.device.deviceId}`}
        value={camera.device.deviceId}
      >
        {camera.device.label}
      </option>
    );
  };

  return getUserMediaError ? (
    <VideochatUserMediaError />
  ) : (
    <React.Fragment>
      <header>
        <VideochatHeader />
      </header>
      <div className="container">
        <form className="hair-check" onSubmit={join}>
          <h1>
            Please enter your Name and select your Microphone, Speakers and
            Camera.{" "}
          </h1>
          {localParticipant && (
            <DailyVideo
              className="hair-check-video-preview-center"
              sessionId={localParticipant.session_id}
              mirror
            />
          )}
          <div>
            <label htmlFor="username">Your Name:</label>
            <input
              name="username"
              type="text"
              placeholder="Enter username"
              onChange={(e) => onChange(e)}
              value={localParticipant?.user_name || " "}
            />
          </div>
          <div>
            <label htmlFor="micOptions">Microphone:</label>
            <select
              name="micOptions"
              id="micSelect"
              onChange={updateMicrophone}
            >
              {microphones?.map(microphonesMapper)}
            </select>
          </div>
          <div>
            <label htmlFor="speakersOptions">Speakers:</label>
            <select
              name="speakersOptions"
              id="speakersSelect"
              onChange={updateSpeakers}
            >
              {speakers?.map(speakersMapper)}
            </select>
          </div>
          <div>
            <label htmlFor="cameraOptions">Camera:</label>
            <select
              name="cameraOptions"
              id="cameraSelect"
              onChange={updateCamera}
            >
              {cameras?.map(camerasMapper)}
            </select>
          </div>

          <button onClick={join} className="button-turquoise" type="submit">
            Join call
          </button>
          <button
            onClick={cancelCall}
            className="cancel-call button-turquoise"
            type="button"
          >
            Back to start
          </button>
        </form>
      </div>
    </React.Fragment>
  );
}

VideochatHairCheck.propTypes = {
  joinCall: PropTypes.func.isRequired,
  cancelCall: PropTypes.func.isRequired,
};
