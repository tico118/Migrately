import React from "react";
import { DailyVideo } from "@daily-co/daily-react";
import VideochatUsername from "./VideochatUsername";
import PropTypes from "prop-types";
import "../videochat/videochatstyles.css";

export default function VideochatTile(props) {
  const id = props.id;
  const isScreenShare = props.isScreenShare;
  const isLocal = props.isLocal;
  const isAlone = props.isAlone;

  let containerCssClasses = isScreenShare ? "tile-screenshare" : "tile-video";

  if (isLocal) {
    containerCssClasses += " self-view";
    if (isAlone) {
      containerCssClasses += " alone";
    }
  }

  return (
    <div className={containerCssClasses}>
      <DailyVideo
        automirror
        sessionId={id}
        type={isScreenShare ? "screenVideo" : "video"}
      />
      <VideochatUsername id={id} isLocal={isLocal} />
    </div>
  );
}

VideochatTile.propTypes = {
  id: PropTypes.string.isRequired,
  isScreenShare: PropTypes.bool,
  isLocal: PropTypes.bool,
  isAlone: PropTypes.bool,
};
