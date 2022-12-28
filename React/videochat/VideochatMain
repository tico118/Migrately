import React, { useEffect, useState, useCallback } from "react";
import DailyIframe from "@daily-co/daily-js";
import { DailyProvider } from "@daily-co/daily-react";
import { roomUrlFromPageUrl, pageUrlFromRoomUrl } from "./VideochatUtils";
import VideochatHomeScreen from "./VideochatHomescreen";
import VideochatCall from "./VideochatCall";
import VideochatTray from "./VideochatTray";
import VideochatHairCheck from "./VideochatHairCheck";
import VideochatHeader from "./VideochatHeader";
import * as videochatService from "../../services/videochatService";
import debug from "sabio-debug";
import PropTypes, { string } from "prop-types";
import "../videochat/videochatstyles.css";

const _logger = debug.extend("VideochatMain");

const STATE_IDLE = "STATE_IDLE";
const STATE_CREATING = "STATE_CREATING";
const STATE_JOINING = "STATE_JOINING";
const STATE_JOINED = "STATE_JOINED";
const STATE_LEAVING = "STATE_LEAVING";
const STATE_ERROR = "STATE_ERROR";
const STATE_HAIRCHECK = "STATE_HAIRCHECK";

export default function VideochatMain(props) {
  const [videochatState, setVideochatState] = useState(STATE_IDLE);
  const [roomUrl, setRoomUrl] = useState(null);
  const [callObject, setCallObject] = useState(null);
  const [apiError, setApiError] = useState(false);
  const [countdownTime, setCountdownTime] = useState(0);
  const [roomName, setRoomName] = useState(null);

  const currentUser = props.currentUser;

  const createCall = useCallback(() => {
    setVideochatState(STATE_CREATING);
    return videochatService
      .createRoom()
      .then(onCreateRoomSuccess)
      .catch(onCreateRoomError);
  }, []);

  const onCreateRoomSuccess = (response) => {
    _logger("From Create Room Success", response);
    setRoomName((prevState) => {
      var rn = { ...prevState };
      rn = response.item.name;
      return rn;
    });
    return response.item.url;
  };

  const onCreateRoomError = (error) => {
    _logger("From Create Room Error", error);
    setRoomUrl(null);
    setVideochatState(STATE_IDLE);
    setApiError(true);
  };

  const onGetMeetingInfoSuccess = (response) => {
    const meetingInfo = response.item.data[0];
    _logger(meetingInfo, "Meeting Information from Get Meeting Success");

    if (roomName !== null) {
      const meetingPayload = {
        hostId: currentUser.id,
        dailyRoomName: meetingInfo.room,
        duration: meetingInfo.duration,
        startTime: meetingInfo.start_Time,
      };

      videochatService
        .addMeeting(meetingPayload)
        .then(onAddMeetingSuccess)
        .catch(onAddMeetingError);
    } else {
      _logger(meetingInfo, "From Participant Side Get Meeting Info Succes");

      const participantPayload = {
        userId: currentUser.id,
        dailyRoomName: meetingInfo.room,
        duration: meetingInfo.participants[1].duration,
        timeJoined: meetingInfo.participants[1].join_Time,
      };
      videochatService
        .addParticipant(participantPayload)
        .then(onAddParticipantSuccess)
        .catch(onAddParticipantError);
    }
  };

  const onGetMeetingInfoError = (error) => {
    _logger(error, "From Get Meeting Info Error");
  };

  const onAddMeetingSuccess = (response) => {
    _logger(response, "Response From Add Meeting Success");
  };

  const onAddMeetingError = (error) => {
    _logger(error, "Response From Add Meeting Error");
  };

  const onAddParticipantSuccess = (response) => {
    _logger(response, "From Add Participant Success");
  };

  const onAddParticipantError = (error) => {
    _logger(error, "From Add Participant Error");
  };

  const startHairCheck = useCallback(async (url) => {
    const newCallObject = DailyIframe.createCallObject();
    setRoomUrl(url);
    setCallObject(newCallObject);
    setVideochatState(STATE_HAIRCHECK);
    await newCallObject.preAuth({ url });
    await newCallObject.startCamera();
    const queryParams = new URLSearchParams(window.location.search);
    const urlForRoom = queryParams.get("roomUrl");
    const roomName = urlForRoom.split("/");
    _logger(roomName);
    videochatService
      .getRoomByName(roomName[3])
      .then(onGetRoomSuccess)
      .catch(onGetRoomError);
  }, []);

  const joinCall = useCallback(() => {
    callObject.join({ url: roomUrl });
  }, [callObject, roomUrl]);

  const onGetRoomSuccess = (response) => {
    const time = new Date(response.item.created_At).getTime();
    const timePlusAnHour = time + 3600000;
    _logger(timePlusAnHour, "After Adding an Hour");
    setCountdownTime(timePlusAnHour);
  };

  const onGetRoomError = (error) => {
    _logger(error);
  };

  const startLeavingCall = useCallback(() => {
    if (!callObject) return;
    if (videochatState === STATE_ERROR) {
      callObject.destroy().then(() => {
        setRoomUrl(null);
        setCallObject(null);
        setVideochatState(STATE_IDLE);
      });
    } else {
      setVideochatState(STATE_LEAVING);

      callObject.leave();
      if (roomName !== null) {
        videochatService
          .getRoomMeetingInfo(roomName)
          .then(onGetMeetingInfoSuccess)
          .catch(onGetMeetingInfoError);
      } else {
        const queryParams = new URLSearchParams(window.location.search);
        const urlForRoom = queryParams.get("roomUrl");
        const roomName = urlForRoom.split("/");
        _logger(roomName);
        videochatService
          .getRoomMeetingInfo(roomName[3])
          .then(onGetMeetingInfoSuccess)
          .catch(onGetMeetingInfoError);
      }
    }
  }, [callObject, videochatState]);

  useEffect(() => {
    const url = roomUrlFromPageUrl();
    if (url) {
      startHairCheck(url);
    }
  }, [startHairCheck]);

  useEffect(() => {
    const pageUrl = pageUrlFromRoomUrl(roomUrl);
    if (pageUrl === window.location.href) return;
    window.history.replaceState(null, null, pageUrl);
  }, [roomUrl]);

  useEffect(() => {
    if (!callObject) return;

    const events = ["joined-meeting", "left-meeting", "error", "camera-error"];

    function handleNewMeetingState() {
      switch (callObject.meetingState()) {
        case "joined-meeting":
          setVideochatState(STATE_JOINED);
          break;
        case "left-meeting":
          callObject.destroy().then(() => {
            setRoomUrl(null);
            setCallObject(null);
            setVideochatState(STATE_IDLE);
          });
          break;
        case "error":
          setVideochatState(STATE_ERROR);
          break;
        default:
          break;
      }
    }

    handleNewMeetingState();

    events.forEach((event) => callObject.on(event, handleNewMeetingState));

    return () => {
      events.forEach((event) => callObject.off(event, handleNewMeetingState));
    };
  }, [callObject]);

  const showCall =
    !apiError &&
    [STATE_JOINING, STATE_JOINED, STATE_ERROR].includes(videochatState);

  const showHairCheck = !apiError && videochatState === STATE_HAIRCHECK;

  const renderApp = () => {
    if (apiError) {
      return (
        <div className="api-error">
          <h1>Error</h1>
          <p>Room could not be created.</p>
        </div>
      );
    }

    if (showHairCheck) {
      return (
        <DailyProvider callObject={callObject}>
          <VideochatHairCheck
            joinCall={joinCall}
            cancelCall={startLeavingCall}
          />
        </DailyProvider>
      );
    }

    if (showCall) {
      return (
        <DailyProvider callObject={callObject}>
          <VideochatHeader />
          <VideochatCall />
          <VideochatTray
            leaveCall={startLeavingCall}
            countdownTime={countdownTime}
          />
        </DailyProvider>
      );
    }

    return (
      <VideochatHomeScreen
        createCall={createCall}
        startHairCheck={startHairCheck}
        currentUser={currentUser}
      />
    );
  };

  return <div className="app">{renderApp()}</div>;
}

VideochatMain.propTypes = {
  currentUser: PropTypes.shape({
    id: PropTypes.number.isRequired,
    roles: PropTypes.arrayOf(string).isRequired,
    isLoggedIn: PropTypes.bool.isRequired,
    email: PropTypes.string,
  }),
};
