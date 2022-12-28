import React, { useCallback, useState } from "react";
import { useAppMessage, useLocalParticipant } from "@daily-co/daily-react";
import PropTypes from "prop-types";
import { Arrow } from "./VideochatIcons";
import "../videochat/videochatstyles.css";

export default function VideochatChat(props) {
  const showChat = props.isShowChat;
  const toggleChat = props.toggleChat;

  const localParticipant = useLocalParticipant();
  const [messages, setMessages] = useState([]);
  const [inputValue, setInputValue] = useState("");

  const sendAppMessage = useAppMessage({
    onAppMessage: useCallback(
      (ev) =>
        setMessages((existingMessages) => [
          ...existingMessages,
          {
            msg: ev.data.msg,
            name: ev.data.name,
          },
        ]),
      []
    ),
  });

  const sendMessage = useCallback(
    (message) => {
      sendAppMessage(
        {
          msg: message,
          name: localParticipant?.user_name || "Guest",
        },
        "*"
      );

      setMessages([
        ...messages,
        {
          msg: message,
          name: localParticipant?.user_name || "Guest",
        },
      ]);
    },
    [localParticipant, messages, sendAppMessage]
  );

  const onChange = (e) => {
    setInputValue(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!inputValue) return;
    sendMessage(inputValue);
    setInputValue("");
  };

  const messagesMapper = (message, index) => {
    return (
      <li key={`message-${index}`} className="chat-message">
        <span className="chat-message-author">{message?.name}</span>:{" "}
        <p className="chat-message-body">{message?.msg}</p>
      </li>
    );
  };
  return showChat ? (
    <aside className="chat">
      <button onClick={toggleChat} className="close-chat" type="button">
        Close chat
      </button>
      <ul className="chat-messages">{messages?.map(messagesMapper)}</ul>
      <div className="add-message">
        <form className="chat-form" onSubmit={handleSubmit}>
          <input
            className="chat-input"
            type="text"
            placeholder="Type your message here.."
            value={inputValue}
            onChange={(e) => onChange(e)}
          />
          <button type="submit" className="chat-submit-button">
            <Arrow />
          </button>
        </form>
      </div>
    </aside>
  ) : null;
}

VideochatChat.propTypes = {
  isShowChat: PropTypes.bool.isRequired,
  toggleChat: PropTypes.func.isRequired,
};
