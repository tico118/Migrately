import React, { useCallback, useState } from "react";
import {
  useDaily,
  useScreenShare,
  useLocalParticipant,
  useVideoTrack,
  useAudioTrack,
  useDailyEvent,
} from "@daily-co/daily-react";
import VideochatMeetingInformation from "./VideochatMeetingInformation";
import VideochatChat from "./VideochatChat";
import {
  CameraOn,
  Leave,
  CameraOff,
  MicrophoneOff,
  MicrophoneOn,
  Screenshare,
  Info,
  ChatIcon,
  ChatHighlighted,
} from "./VideochatIcons";
import PropTypes from "prop-types";
import Countdown from "react-countdown";
import "../videochat/videochatstyles.css";

export default function VideochatTray(props) {
  const leaveCall = props.leaveCall;
  const countdownTime = props.countdownTime;

  const callObject = useDaily();
  const { isSharingScreen, startScreenShare, stopScreenShare } =
    useScreenShare();

  const [showMeetingInformation, setShowMeetingInformation] = useState(false);
  const [showChat, setShowChat] = useState(false);
  const [newChatMessage, setNewChatMessage] = useState(false);

  const localParticipant = useLocalParticipant();
  const localVideo = useVideoTrack(localParticipant?.session_id);
  const localAudio = useAudioTrack(localParticipant?.session_id);
  const mutedVideo = localVideo.isOff;
  const mutedAudio = localAudio.isOff;

  useDailyEvent(
    "app-message",
    useCallback(() => {
      if (!showChat) {
        setNewChatMessage(true);
      }
    }, [showChat])
  );

  const toggleVideo = useCallback(() => {
    callObject.setLocalVideo(mutedVideo);
  }, [callObject, mutedVideo]);

  const toggleAudio = useCallback(() => {
    callObject.setLocalAudio(mutedAudio);
  }, [callObject, mutedAudio]);

  const toggleScreenShare = () =>
    isSharingScreen ? stopScreenShare() : startScreenShare();

  const toggleMeetingInformation = () => {
    setShowMeetingInformation(!showMeetingInformation);
  };

  const toggleChat = () => {
    setShowChat(!showChat);
    if (newChatMessage) {
      setNewChatMessage(!newChatMessage);
    }
  };

  return (
    <div className="tray">
      {showMeetingInformation && <VideochatMeetingInformation />}

      <VideochatChat isShowChat={showChat} toggleChat={toggleChat} />
      <div className="tray-buttons-container">
        <div className="controls">
          <button
            className="button-turquoise"
            onClick={toggleVideo}
            type="button"
          >
            {mutedVideo ? <CameraOff /> : <CameraOn />}
            {mutedVideo ? "Turn camera on" : "Turn camera off"}
          </button>
          <button
            className="button-turquoise"
            onClick={toggleAudio}
            type="button"
          >
            {mutedAudio ? <MicrophoneOff /> : <MicrophoneOn />}
            {mutedAudio ? "Unmute mic" : "Mute mic"}
          </button>
        </div>
        <div className="actions">
          <button
            className="button-turquoise"
            onClick={toggleScreenShare}
            type="button"
          >
            <Screenshare />
            {isSharingScreen ? "Stop sharing screen" : "Share screen"}
          </button>
          <button
            className="button-turquoise"
            onClick={toggleMeetingInformation}
            type="button"
          >
            <Info />
            {showMeetingInformation ? "Hide info" : "Show info"}
          </button>
          <button
            className="button-turquoise"
            onClick={toggleChat}
            type="button"
          >
            {newChatMessage ? <ChatHighlighted /> : <ChatIcon />}
            {showChat ? "Hide chat" : "Show chat"}
          </button>
        </div>
        <div className="leave">
          <div className="countdown">
            <span>Call Expires:</span>
            <Countdown date={countdownTime} />
          </div>
          <button
            className="button-turquoise"
            onClick={leaveCall}
            type="button"
          >
            <Leave /> Leave call
          </button>
        </div>
      </div>
    </div>
  );
}

VideochatTray.propTypes = {
  leaveCall: PropTypes.func.isRequired,
  countdownTime: PropTypes.number,
};
