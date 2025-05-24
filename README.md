# music-skill-check
A simple web app to test rhythm sense, pitch recognition, and finger agility using interactive audio and timing challenges.
// App.jsx
import React from "react";
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import Home from "./pages/Home";
import PitchTest from "./pages/PitchTest";
import RhythmTest from "./pages/RhythmTest";
import FingerTest from "./pages/FingerTest";
import Results from "./pages/Results";

export default function App() {
  return (
    <Router>
      <div className="p-4 font-sans">
        <nav className="flex gap-4 mb-4">
          <Link to="/" className="text-blue-500">首頁</Link>
          <Link to="/pitch" className="text-blue-500">音感測驗</Link>
          <Link to="/rhythm" className="text-blue-500">節奏測驗</Link>
          <Link to="/finger" className="text-blue-500">手指靈敏度</Link>
        </nav>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/pitch" element={<PitchTest />} />
          <Route path="/rhythm" element={<RhythmTest />} />
          <Route path="/finger" element={<FingerTest />} />
          <Route path="/results" element={<Results />} />
        </Routes>
      </div>
    </Router>
  );
}

// pages/Home.jsx
export default function Home() {
  return (
    <div className="text-center">
      <h1 className="text-3xl font-bold mb-4">🎵 音樂能力測驗平台</h1>
      <p>測驗你的音感、節奏感與手指靈敏度！</p>
    </div>
  );
}

// pages/PitchTest.jsx
import React, { useState } from "react";

const pitches = ["Do", "Re", "Mi", "Fa", "So", "La", "Ti"];

export default function PitchTest() {
  const [question, setQuestion] = useState(generateNote());
  const [feedback, setFeedback] = useState("");

  function generateNote() {
    return pitches[Math.floor(Math.random() * pitches.length)];
  }

  function playNote(note) {
    const audio = new Audio(`/audio/${note}.mp3`); // 需放音檔
    audio.play();
  }

  function checkAnswer(answer) {
    if (answer === question) {
      setFeedback("✅ 正確！");
    } else {
      setFeedback(`❌ 錯誤，正確是 ${question}`);
    }
    setTimeout(() => {
      setQuestion(generateNote());
      setFeedback("");
    }, 1500);
  }

  return (
    <div>
      <h2 className="text-xl font-semibold">音感測驗</h2>
      <button
        onClick={() => playNote(question)}
        className="mt-4 bg-blue-500 text-white px-4 py-2 rounded"
      >
        播放音高
      </button>
      <div className="mt-4 grid grid-cols-4 gap-2">
        {pitches.map((p) => (
          <button
            key={p}
            onClick={() => checkAnswer(p)}
            className="bg-gray-200 p-2 rounded hover:bg-gray-300"
          >
            {p}
          </button>
        ))}
      </div>
      <p className="mt-4 text-lg">{feedback}</p>
    </div>
  );
}

// pages/RhythmTest.jsx
import React, { useState, useRef } from "react";

export default function RhythmTest() {
  const [beats] = useState([0, 1000, 2000, 3000]);
  const [results, setResults] = useState([]);
  const startTimeRef = useRef(null);

  function startTest() {
    startTimeRef.current = Date.now();
    new Audio("/audio/clap.mp3").play();
    beats.slice(1).forEach((delay) => {
      setTimeout(() => new Audio("/audio/clap.mp3").play(), delay);
    });
  }

  function handleClick() {
    const now = Date.now();
    const elapsed = now - startTimeRef.current;
    setResults((prev) => [...prev, elapsed]);
  }

  return (
    <div>
      <h2 className="text-xl font-semibold">節奏感測驗</h2>
      <button
        onClick={startTest}
        className="mt-4 bg-green-500 text-white px-4 py-2 rounded"
      >
        開始節奏
      </button>
      <button
        onClick={handleClick}
        className="ml-4 bg-gray-500 text-white px-4 py-2 rounded"
      >
        跟著點擊
      </button>
      <div className="mt-4">
        <p>你的點擊時間（毫秒）：</p>
        <ul>
          {results.map((r, i) => (
            <li key={i}>{r} ms</li>
          ))}
        </ul>
      </div>
    </div>
  );
}

// pages/FingerTest.jsx
import React, { useState, useEffect } from "react";

export default function FingerTest() {
  const [key, setKey] = useState("W");
  const [score, setScore] = useState(0);
  const [timeLeft, setTimeLeft] = useState(10);

  useEffect(() => {
    const handleKeyPress = (e) => {
      if (e.key.toUpperCase() === key) {
        setScore((s) => s + 1);
        newKey();
      }
    };
    window.addEventListener("keydown", handleKeyPress);
    return () => window.removeEventListener("keydown", handleKeyPress);
  }, [key]);

  useEffect(() => {
    if (timeLeft <= 0) return;
    const timer = setTimeout(() => setTimeLeft((t) => t - 1), 1000);
    return () => clearTimeout(timer);
  }, [timeLeft]);

  function newKey() {
    const keys = ["W", "A", "S", "D"];
    setKey(keys[Math.floor(Math.random() * keys.length)]);
  }

  useEffect(() => {
    newKey();
  }, []);

  return (
    <div>
      <h2 className="text-xl font-semibold">手指靈敏度測驗</h2>
      <p className="text-lg mt-2">請按下：<span className="font-bold">{key}</span></p>
      <p className="mt-2">剩餘時間：{timeLeft} 秒</p>
      <p className="mt-2">分數：{score}</p>
    </div>
  );
}

// pages/Results.jsx
export default function Results() {
  return (
    <div>
      <h2 className="text-xl font-semibold">測驗結果（暫無儲存功能）</h2>
      <p>每項測驗完成後可手動查看表現。</p>
    </div>
  );
}
