import React from "react";
import { useParticipantProperty } from "@daily-co/daily-react";
import PropTypes from "prop-types";
import "../videochat/videochatstyles.css";

export default function VideochatUsername(props) {
  const id = props.id;
  const isLocal = props.isLocal;

  const username = useParticipantProperty(id, "user_name");

  return (
    <div className="username">
      {username || id} {isLocal && "(you)"}
    </div>
  );
}

VideochatUsername.propTypes = {
  id: PropTypes.string.isRequired,
  isLocal: PropTypes.bool,
};
