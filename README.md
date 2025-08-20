import React, { useState, useRef, useEffect } from 'react';
import { ChevronLeft, Mic, MicOff, Send, MessageCircle, Copy, X, Share2, CheckCircle, Globe, Users, Volume2, Wifi, QrCode } from 'lucide-react';

const App = () => {
  const [currentPage, setCurrentPage] = useState('qrconnect');
  const [isListening, setIsListening] = useState(false);
  const [showChat, setShowChat] = useState(false);
  const [chatInput, setChatInput] = useState('');
  const [showSummary, setShowSummary] = useState(false);
  const [summaryType, setSummaryType] = useState('');
  const [copySuccess, setCopySuccess] = useState(false);
  const [participantCount, setParticipantCount] = useState(1);
  const [myLanguage, setMyLanguage] = useState('English');
  const [showLanguageSelector, setShowLanguageSelector] = useState(false);
  const chatEndRef = useRef(null);

  // Multi-participant conversation data
  const [participants, setParticipants] = useState([
    { id: 1, name: 'You', language: 'English', speaking: false, host: true },
    { id: 2, name: 'Rahul Sharma', language: 'Hindi', speaking: false },
    { id: 3, name: 'Marie Dubois', language: 'French', speaking: false },
    { id: 4, name: 'Yuki Tanaka', language: 'Japanese', speaking: false }
  ]);

  // Conversation history with multiple speakers
  const [conversationHistory, setConversationHistory] = useState([
    { 
      speaker: 'Rahul Sharma', 
      originalLang: 'Hindi',
      original: 'मैं राहुल शर्मा हूं, और आज हम नई सौर ऊर्जा परियोजना के बारे में चर्चा करेंगे।',
      translated: "I am Rahul Sharma, and today we will discuss about the new solar energy project.",
      timestamp: '2:29 PM'
    },
    {
      speaker: 'Marie Dubois',
      originalLang: 'French', 
      original: "Bonjour, je suis Marie de l'équipe technique. Le budget est-il confirmé?",
      translated: "Hello, I'm Marie from the technical team. Is the budget confirmed?",
      timestamp: '2:30 PM'
    },
    {
      speaker: 'You',
      originalLang: 'English',
      original: "Yes, we have 50 crore rupees approved for the entire project.",
      translated: "Yes, we have 50 crore rupees approved for the entire project.",
      timestamp: '2:30 PM'
    },
    {
      speaker: 'Yuki Tanaka',
      originalLang: 'Japanese',
      original: "プロジェクトの期限はいつですか？私たちのチームは準備ができています。",
      translated: "What is the project deadline? Our team is ready.",
      timestamp: '2:31 PM'
    }
  ]);

  const languages = [
    'English', 'Hindi', 'French', 'Spanish', 'German', 
    'Japanese', 'Chinese', 'Arabic', 'Portuguese', 'Russian',
    'Korean', 'Italian', 'Dutch', 'Turkish', 'Polish'
  ];

  // Simulated data for smart features
  const conversationData = {
    speakers: [
      { name: "Rahul Sharma", role: "Project Manager", language: "Hindi" },
      { name: "Marie Dubois", role: "Technical Lead", language: "French" },
      { name: "Yuki Tanaka", role: "Engineering Director", language: "Japanese" }
    ],
    topics: {
      project: "Solar Energy Initiative",
      budget: "50 crore rupees",
      timeline: "Q2 2025",
      location: "Gujarat and Rajasthan",
      capacity: "100 MW",
      phases: 3,
      teamSize: 45
    },
    keyPoints: [
      "Solar panel installation across 5 sites",
      "International team collaboration",
      "Budget approved by all stakeholders",
      "Technology transfer from Japanese partners",
      "French team handling technical implementation"
    ],
    actionItems: [
      "Rahul to finalize vendor contracts",
      "Marie to review technical specifications",
      "Yuki to coordinate engineering resources",
      "Schedule follow-up in all time zones",
      "Prepare multilingual documentation"
    ]
  };

  const [chatMessages, setChatMessages] = useState([]);

  useEffect(() => {
    chatEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [chatMessages]);

  useEffect(() => {
    if (showChat && chatMessages.length === 0) {
      setChatMessages([{
        id: 1,
        type: 'bot',
        text: "Hello! I'm monitoring this multi-language conversation. I can help you understand what everyone is saying, regardless of their language. Ask me about any participant, topic, or get summaries!"
      }]);
    }
  }, [showChat, chatMessages.length]);

  useEffect(() => {
    // Simulate participants joining
    if (currentPage === 'connected') {
      const timer1 = setTimeout(() => setParticipantCount(2), 1000);
      const timer2 = setTimeout(() => setParticipantCount(3), 2500);
      const timer3 = setTimeout(() => setParticipantCount(4), 4000);
      return () => {
        clearTimeout(timer1);
        clearTimeout(timer2);
        clearTimeout(timer3);
      };
    }
  }, [currentPage]);

  // Simulate active speakers
  useEffect(() => {
    if (currentPage === 'translate' && isListening) {
      const interval = setInterval(() => {
        const randomParticipant = Math.floor(Math.random() * 4) + 1;
        setParticipants(prev => prev.map(p => ({
          ...p,
          speaking: p.id === randomParticipant
        })));
      }, 3000);
      return () => clearInterval(interval);
    } else {
      setParticipants(prev => prev.map(p => ({ ...p, speaking: false })));
    }
  }, [currentPage, isListening]);

  const handleConnect = () => {
    setCurrentPage('connected');
  };

  const handleTranslate = () => {
    setCurrentPage('translate');
  };

  const handleSmartFeatures = () => {
    setShowChat(true);
  };

  const toggleListening = () => {
    setIsListening(!isListening);
    if (!isListening) {
      // Simulate new conversation entry
      setTimeout(() => {
        setConversationHistory(prev => [...prev, {
          speaker: 'Rahul Sharma',
          originalLang: 'Hindi',
          original: 'हमें अगले सप्ताह तक सभी अनुमोदन चाहिए।',
          translated: 'We need all approvals by next week.',
          timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
        }]);
      }, 2000);
    }
  };

  const handleSummarize = (type) => {
    setSummaryType(type);
    setShowSummary(true);
  };

  const handleCopyURL = () => {
    navigator.clipboard.writeText('https://antar.link/s/CUCUGHG40');
    setCopySuccess(true);
    setTimeout(() => setCopySuccess(false), 2000);
  };

  const handleSendMessage = (e) => {
    if (e) e.preventDefault();
    
    const messageText = chatInput.trim();
    if (!messageText) return;

    const userMessage = {
      id: Date.now(),
      type: 'user',
      text: messageText
    };

    setChatMessages(prev => [...prev, userMessage]);
    const currentInput = messageText.toLowerCase();
    setChatInput('');

    // Enhanced bot responses for multi-participant context
    setTimeout(() => {
      let botResponse = '';

      if (currentInput.includes('who') && (currentInput.includes('speaking') || currentInput.includes('participants'))) {
        botResponse = "There are 4 participants in this conversation:\n• You (English)\n• Rahul Sharma (Hindi) - Project Manager\n• Marie Dubois (French) - Technical Lead\n• Yuki Tanaka (Japanese) - Engineering Director\n\nEveryone can speak in their native language and hear others in their preferred language.";
      }
      else if (currentInput.includes('rahul') || (currentInput.includes('his') && currentInput.includes('name'))) {
        botResponse = "Rahul Sharma is the Project Manager, speaking in Hindi. He mentioned the solar energy project with a budget of 50 crore rupees and needs approvals by next week.";
      }
      else if (currentInput.includes('marie') || currentInput.includes('french')) {
        botResponse = "Marie Dubois from the technical team is participating from France. She asked about budget confirmation and will be handling technical implementation.";
      }
      else if (currentInput.includes('yuki') || currentInput.includes('japanese')) {
        botResponse = "Yuki Tanaka is the Engineering Director from the Japanese team. He asked about project deadlines and confirmed his team is ready to begin.";
      }
      else if (currentInput.includes('language') || currentInput.includes('translation')) {
        botResponse = "Each participant is speaking in their native language:\n• Rahul - Hindi\n• Marie - French\n• Yuki - Japanese\n• You - English\n\nThe system translates everything in real-time so everyone understands each other perfectly!";
      }
      else if (currentInput.includes('how') && (currentInput.includes('work') || currentInput.includes('this'))) {
        botResponse = "Here's how it works:\n1. You created a session with the QR code\n2. Each person scanned it with their device\n3. They selected their preferred language\n4. Now everyone speaks naturally in their own language\n5. Everyone hears others in their selected language\n6. All translation happens instantly in real-time!";
      }
      else if (currentInput.includes('budget') || currentInput.includes('cost')) {
        botResponse = "The project budget of 50 crore rupees was confirmed by all participants. Marie from the French team specifically asked for confirmation, and it was approved.";
      }
      else if (currentInput.includes('summary') || currentInput.includes('main points')) {
        botResponse = "Multi-language Meeting Summary:\n\n📍 Participants: 4 people (Hindi, French, Japanese, English)\n💰 Budget: ₹50 crore (confirmed)\n📅 Timeline: Q2 2025, approvals needed by next week\n🌍 International collaboration confirmed\n✅ All teams ready to proceed\n\nKey advantage: Everyone communicated in their native language with perfect understanding!";
      }
      else {
        botResponse = "I'm tracking this 4-way conversation in Hindi, French, Japanese, and English. Each person is speaking their native language and hearing translations in real-time. What would you like to know about the discussion or participants?";
      }

      const botMessage = {
        id: Date.now() + 1,
        type: 'bot',
        text: botResponse
      };
      setChatMessages(prev => [...prev, botMessage]);
    }, 800);
  };

  // QR Connect Page
  const QRConnectPage = () => (
    <div className="min-h-screen bg-gradient-to-b from-purple-900 to-purple-700 flex flex-col">
      <div className="p-4">
        <div className="flex items-center justify-between mb-2">
          <div className="text-white text-xs">2:29</div>
          <div className="flex items-center gap-1">
            <div className="text-white text-xs">LTE</div>
            <div className="w-4 h-3 border border-white rounded-sm">
              <div className="w-3 h-2 bg-white rounded-sm m-0.5"></div>
            </div>
          </div>
        </div>
      </div>

      <div className="flex-1 flex flex-col items-center justify-center px-6">
        <div className="bg-purple-800/50 backdrop-blur rounded-3xl p-8 w-full max-w-sm">
          <div className="flex items-center justify-center gap-2 mb-4">
            <Globe className="w-6 h-6 text-purple-300" />
            <h2 className="text-white text-xl font-semibold">Universal Translator</h2>
          </div>
          
          <p className="text-purple-200 text-sm text-center mb-6">
            Anyone can scan to join - speak any language, understand everyone
          </p>
          
          <div className="bg-white rounded-2xl p-6 mb-4 relative">
            <div className="absolute top-2 right-2 bg-green-500 text-white text-xs px-2 py-1 rounded flex items-center gap-1">
              <Wifi className="w-3 h-3" />
              LIVE
            </div>
            <svg viewBox="0 0 200 200" className="w-full h-auto">
              <rect fill="#000" x="10" y="10" width="60" height="60"/>
              <rect fill="#fff" x="20" y="20" width="40" height="40"/>
              <rect fill="#000" x="30" y="30" width="20" height="20"/>
              
              <rect fill="#000" x="130" y="10" width="60" height="60"/>
              <rect fill="#fff" x="140" y="20" width="40" height="40"/>
              <rect fill="#000" x="150" y="30" width="20" height="20"/>
              
              <rect fill="#000" x="10" y="130" width="60" height="60"/>
              <rect fill="#fff" x="20" y="140" width="40" height="40"/>
              <rect fill="#000" x="30" y="150" width="20" height="20"/>
              
              <rect fill="#000" x="80" y="80" width="10" height="30"/>
              <rect fill="#000" x="100" y="80" width="20" height="10"/>
              <rect fill="#000" x="90" y="90" width="30" height="10"/>
              <rect fill="#000" x="110" y="100" width="10" height="20"/>
              <rect fill="#000" x="80" y="110" width="30" height="10"/>
            </svg>
          </div>

          <div className="bg-purple-700/30 rounded-xl p-3 mb-4">
            <div className="flex items-center justify-between mb-2">
              <span className="text-purple-300 text-xs">Quick Join URL</span>
              <Users className="w-4 h-4 text-purple-400" />
            </div>
            <p className="text-white text-sm font-mono break-all">https://antar.link/s/CUCUGHG40</p>
          </div>

          <button 
            onClick={handleCopyURL}
            className="flex items-center justify-center gap-2 text-purple-300 hover:text-white text-sm mx-auto mb-6 transition-colors"
          >
            {copySuccess ? (
              <>
                <CheckCircle className="w-4 h-4 text-green-400" />
                <span className="text-green-400">Copied!</span>
              </>
            ) : (
              <>
                <Copy className="w-4 h-4" />
                <span>Share Link</span>
              </>
            )}
          </button>

          <button
            onClick={handleConnect}
            className="w-full bg-gradient-to-r from-purple-600 to-pink-600 text-white py-4 rounded-2xl font-semibold transition-all transform hover:scale-105 flex items-center justify-center gap-2"
          >
            <QrCode className="w-5 h-5" />
            Start Session
          </button>

          <div className="mt-4 text-center">
            <p className="text-purple-300 text-xs">No app needed for participants</p>
            <p className="text-purple-400 text-xs">Works with any device • Unlimited participants</p>
          </div>
        </div>
      </div>
    </div>
  );

  // Connected Status Page with participant joining animation
  const ConnectedPage = () => (
    <div className="min-h-screen bg-gradient-to-b from-purple-900 to-purple-700 flex flex-col">
      <div className="p-4">
        <div className="flex items-center justify-between mb-2">
          <div className="text-white text-xs">2:29</div>
          <div className="flex items-center gap-1">
            <div className="text-white text-xs">LTE</div>
            <div className="w-4 h-3 border border-white rounded-sm">
              <div className="w-3 h-2 bg-white rounded-sm m-0.5"></div>
            </div>
          </div>
        </div>
      </div>

      <div className="flex-1 flex flex-col items-center justify-center px-6">
        <div className="bg-purple-800/50 backdrop-blur rounded-3xl p-8 w-full max-w-sm">
          <h2 className="text-white text-2xl font-bold text-center mb-2">Session Active</h2>
          
          <div className="flex items-center justify-center gap-2 mb-4">
            <div className="w-3 h-3 bg-green-400 rounded-full animate-pulse"></div>
            <span className="text-green-400 text-sm">Broadcasting</span>
          </div>

          {/* Participants joining indicator */}
          <div className="bg-purple-700/50 rounded-xl p-4 mb-6">
            <div className="flex items-center justify-between mb-3">
              <span className="text-purple-200 text-sm">Participants</span>
              <span className="text-white font-bold">{participantCount}/∞</span>
            </div>
            
            <div className="space-y-2">
              {participants.slice(0, participantCount).map((participant, index) => (
                <div key={participant.id} className={`flex items-center justify-between p-2 rounded-lg bg-purple-600/30 transform transition-all ${index === participantCount - 1 ? 'animate-pulse' : ''}`}>
                  <div className="flex items-center gap-2">
                    <div className={`w-2 h-2 rounded-full ${participant.host ? 'bg-yellow-400' : 'bg-green-400'}`}></div>
                    <span className="text-white text-sm">{participant.name}</span>
                    {participant.host && <span className="text-xs bg-purple-600 px-2 py-0.5 rounded text-purple-200">Host</span>}
                  </div>
                  <span className="text-purple-300 text-xs">{participant.language}</span>
                </div>
              ))}
            </div>

            {participantCount < 4 && (
              <div className="mt-2 text-center">
                <span className="text-purple-400 text-xs animate-pulse">Waiting for more participants...</span>
              </div>
            )}
          </div>

          <div className="grid grid-cols-2 gap-3 mb-6">
            <div className="bg-purple-700/30 rounded-xl p-3 text-center">
              <Volume2 className="w-6 h-6 text-purple-300 mx-auto mb-1" />
              <div className="text-lg font-bold text-white">15</div>
              <div className="text-xs text-purple-300">Languages</div>
            </div>
            <div className="bg-purple-700/30 rounded-xl p-3 text-center">
              <Users className="w-6 h-6 text-purple-300 mx-auto mb-1" />
              <div className="text-lg font-bold text-white">{participantCount}</div>
              <div className="text-xs text-purple-300">Connected</div>
            </div>
          </div>

          <div className="bg-purple-700/50 rounded-xl p-3 mb-4">
            <p className="text-center text-purple-200 text-xs mb-1">Session Code</p>
            <p className="text-center text-white font-mono text-lg">YY36EQK2Z</p>
          </div>

          <button
            onClick={handleTranslate}
            className="w-full bg-gradient-to-r from-blue-500 to-purple-600 text-white py-5 rounded-2xl font-semibold mb-3 hover:shadow-lg transition-all transform hover:scale-105 flex flex-col items-center"
          >
            <MessageCircle className="w-7 h-7 mb-1" />
            <span className="text-lg">Start Translating</span>
            <span className="text-xs opacity-80">Real-time multi-language</span>
          </button>

          <button 
            onClick={() => {
              setCurrentPage('translate');
              setTimeout(() => handleSmartFeatures(), 100);
            }}
            className="w-full bg-gradient-to-r from-purple-600 to-pink-600 text-white py-5 rounded-2xl font-semibold hover:shadow-lg transition-all transform hover:scale-105 flex flex-col items-center"
          >
            <div className="w-7 h-7 mb-1 flex items-center justify-center">
              <svg className="w-full h-full" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <circle cx="9" cy="9" r="7"/>
                <circle cx="15" cy="15" r="7"/>
              </svg>
            </div>
            <span className="text-lg">Smart Features</span>
            <span className="text-xs opacity-80">AI conversation insights</span>
          </button>
        </div>
      </div>
    </div>
  );

  // Language Selector Modal
  const LanguageSelector = () => (
    <div className="fixed inset-0 bg-black/50 flex items-center justify-center p-4 z-50">
      <div className="bg-purple-800 rounded-2xl p-6 max-w-sm w-full max-h-[80vh] overflow-y-auto">
        <div className="flex items-center justify-between mb-4">
          <h3 className="text-white text-lg font-semibold">Select Your Language</h3>
          <button onClick={() => setShowLanguageSelector(false)} className="text-white">
            <X className="w-5 h-5" />
          </button>
        </div>
        
        <div className="space-y-2">
          {languages.map((lang) => (
            <button
              key={lang}
              onClick={() => {
                setMyLanguage(lang);
                setShowLanguageSelector(false);
              }}
              className={`w-full text-left px-4 py-3 rounded-lg transition-colors ${
                myLanguage === lang 
                  ? 'bg-purple-600 text-white' 
                  : 'bg-purple-700/30 text-purple-200 hover:bg-purple-700/50'
              }`}
            >
              {lang}
            </button>
          ))}
        </div>
      </div>
    </div>
  );

  // Summary Modal
  const SummaryModal = () => (
    <div className="fixed inset-0 bg-black/50 flex items-center justify-center p-4 z-50">
      <div className="bg-purple-800 rounded-2xl p-6 max-w-sm w-full">
        <div className="flex items-center justify-between mb-4">
          <h3 className="text-white text-lg font-semibold">
            {summaryType === 'points' ? 'Summary Points' : 'To-Do Tasks'}
          </h3>
          <button onClick={() => setShowSummary(false)} className="text-white">
            <X className="w-5 h-5" />
          </button>
        </div>
        
        <div className="space-y-2 mb-4">
          {summaryType === 'points' ? (
            conversationData.keyPoints.map((point, index) => (
              <div key={index} className="flex items-start gap-2">
                <span className="text-green-400 mt-1">•</span>
                <span className="text-white text-sm">{point}</span>
              </div>
            ))
          ) : (
            conversationData.actionItems.map((item, index) => (
              <div key={index} className="flex items-start gap-2">
                <input type="checkbox" className="mt-1" />
                <span className="text-white text-sm">{item}</span>
              </div>
            ))
          )}
        </div>

        <button 
          onClick={() => setShowSummary(false)}
          className="w-full bg-purple-600 text-white py-3 rounded-xl font-medium"
        >
          Done
        </button>
      </div>
    </div>
  );

  // Enhanced Translation Page with multi-participant view
  const TranslatePage = () => (
    <div className="min-h-screen bg-gradient-to-b from-purple-900 to-purple-700 flex flex-col">
      <div className="p-4">
        <div className="flex items-center justify-between mb-2">
          <div className="text-white text-xs">2:29</div>
          <div className="flex items-center gap-1">
            <div className="text-white text-xs">LTE</div>
            <div className="w-4 h-3 border border-white rounded-sm">
              <div className="w-3 h-2 bg-white rounded-sm m-0.5"></div>
            </div>
          </div>
        </div>

        <div className="flex items-center mb-3">
          <button
            onClick={() => setCurrentPage('connected')}
            className="text-white p-2"
          >
            <ChevronLeft className="w-6 h-6" />
          </button>
          <h1 className="text-xl font-semibold text-white ml-2">Multi-Language Session</h1>
          <div className="ml-auto">
            <button
              onClick={handleSmartFeatures}
              className="bg-purple-600 hover:bg-purple-700 text-white px-3 py-1.5 rounded-full text-sm transition-colors"
            >
              Smart AI
            </button>
          </div>
        </div>

        {/* Active participants bar */}
        <div className="bg-purple-800/50 rounded-lg p-2 mb-3">
          <div className="flex items-center justify-between mb-1">
            <div className="flex items-center gap-2">
              <Users className="w-4 h-4 text-purple-300" />
              <span className="text-xs text-purple-300">Active Participants</span>
            </div>
            <button 
              onClick={() => setShowLanguageSelector(true)}
              className="text-xs bg-purple-600 px-2 py-1 rounded text-white"
            >
              My Language: {myLanguage}
            </button>
          </div>
          <div className="flex gap-2 overflow-x-auto">
            {participants.map((p) => (
              <div 
                key={p.id} 
                className={`flex items-center gap-1 px-2 py-1 rounded-full text-xs whitespace-nowrap transition-all ${
                  p.speaking 
                    ? 'bg-green-600 text-white animate-pulse' 
                    : 'bg-purple-700/50 text-purple-200'
                }`}
              >
                {p.speaking && <Volume2 className="w-3 h-3" />}
                {p.name} ({p.language})
              </div>
            ))}
          </div>
        </div>
      </div>

      {!showChat ? (
        <div className="flex-1 flex flex-col px-4">
          <div className="flex-1 space-y-2 overflow-y-auto">
            {conversationHistory.map((entry, index) => (
              <div key={index} className="bg-purple-800/50 backdrop-blur rounded-xl p-3">
                <div className="flex items-center justify-between mb-1">
                  <div className="flex items-center gap-2">
                    <span className="text-sm font-medium text-white">{entry.speaker}</span>
                    <span className="text-xs text-purple-400">({entry.originalLang})</span>
                  </div>
                  <span className="text-xs text-purple-400">{entry.timestamp}</span>
                </div>
                
                {entry.speaker !== 'You' && (
                  <div className="text-xs text-purple-300 mb-1 italic">
                    Original: {entry.original}
                  </div>
                )}
                
                <div className="text-white text-sm">
                  {entry.speaker === 'You' ? (
                    <span className="flex items-center gap-1">
                      <Globe className="w-3 h-3 text-green-400" />
                      {entry.translated}
                    </span>
                  ) : (
                    `Translation: ${entry.translated}`
                  )}
                </div>
              </div>
            ))}
          </div>

          <div className="py-4 space-y-3">
            <div className="flex gap-2">
              <button
                onClick={() => handleSummarize('points')}
                className="flex-1 bg-green-600 hover:bg-green-700 text-white py-2.5 rounded-xl font-medium transition-all transform hover:scale-105 flex items-center justify-center gap-1 text-sm"
              >
                <MessageCircle className="w-4 h-4" />
                Summary
              </button>
              <button
                onClick={() => handleSummarize('tasks')}
                className="flex-1 bg-orange-600 hover:bg-orange-700 text-white py-2.5 rounded-xl font-medium transition-all transform hover:scale-105 flex items-center justify-center gap-1 text-sm"
              >
                <MessageCircle className="w-4 h-4" />
                Action Items
              </button>
            </div>

            <button
              onClick={toggleListening}
              className={`w-full py-4 rounded-full font-semibold text-white transition-all transform hover:scale-105 flex items-center justify-center gap-3 ${
                isListening 
                  ? 'bg-red-600 hover:bg-red-700 animate-pulse' 
                  : 'bg-blue-600 hover:bg-blue-700'
              }`}
            >
              {isListening ? (
                <>
                  <MicOff className="w-6 h-6" />
                  <span>Stop Session</span>
                </>
              ) : (
                <>
                  <Mic className="w-6 h-6" />
                  <span>Start Listening</span>
                </>
              )}
            </button>
          </div>
        </div>
      ) : (
        <SmartFeaturesChat 
          chatMessages={chatMessages}
          chatInput={chatInput}
          setChatInput={setChatInput}
          handleSendMessage={handleSendMessage}
          onBack={() => setShowChat(false)}
          chatEndRef={chatEndRef}
        />
      )}

      {showSummary && <SummaryModal />}
      {showLanguageSelector && <LanguageSelector />}
    </div>
  );

  // Smart Features Chat Component
  const SmartFeaturesChat = ({ chatMessages, chatInput, setChatInput, handleSendMessage, onBack, chatEndRef }) => {
    const handleSubmit = (e) => {
      e.preventDefault();
      handleSendMessage(e);
    };

    const handleKeyDown = (e) => {
      if (e.key === 'Enter' && !e.shiftKey) {
        e.preventDefault();
        handleSendMessage(e);
      }
    };

    return (
      <div className="flex-1 flex flex-col bg-purple-800/30 rounded-t-3xl overflow-hidden">
        <div className="p-4 border-b border-purple-700/50 flex-shrink-0">
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-2">
              <button onClick={onBack} className="text-white p-1">
                <ChevronLeft className="w-6 h-6" />
              </button>
              <h2 className="text-lg font-semibold text-white">Smart AI Assistant</h2>
            </div>
            <div className="flex items-center gap-1">
              <Users className="w-4 h-4 text-purple-300" />
              <span className="text-xs text-purple-300">{participants.length} languages</span>
            </div>
          </div>
        </div>

        <div className="bg-purple-900/50 px-4 py-3 flex-shrink-0">
          <p className="text-center text-purple-200 text-sm">
            Ask about any participant or topic
          </p>
          <p className="text-center text-purple-300 text-xs">
            "Who's speaking?" • "What did Marie say?" • "Summary in English"
          </p>
        </div>

        <div className="flex-1 overflow-y-auto p-4 space-y-3 min-h-0">
          {chatMessages.map((message) => (
            <div
              key={message.id}
              className={`flex ${message.type === 'user' ? 'justify-end' : 'justify-start'}`}
            >
              <div
                className={`max-w-[85%] p-3 rounded-2xl ${
                  message.type === 'user'
                    ? 'bg-purple-600 text-white rounded-br-sm'
                    : 'bg-purple-800/70 text-white rounded-bl-sm'
                }`}
                style={{ whiteSpace: 'pre-line' }}
              >
                {message.text}
              </div>
            </div>
          ))}
          <div ref={chatEndRef} />
        </div>

        <div className="p-4 bg-purple-800/50 flex-shrink-0">
          <form onSubmit={handleSubmit} className="flex gap-2">
            <input
              type="text"
              value={chatInput}
              onChange={(e) => setChatInput(e.target.value)}
              onKeyDown={handleKeyDown}
              placeholder="Ask about participants or conversation..."
              className="flex-1 bg-purple-900/50 text-white placeholder-purple-400 rounded-full px-4 py-3 focus:outline-none focus:ring-2 focus:ring-purple-500"
              autoComplete="off"
              autoFocus
            />
            <button
              type="button"
              onClick={() => handleSendMessage()}
              className="bg-purple-600 hover:bg-purple-700 text-white p-3 rounded-full transition-colors flex-shrink-0"
            >
              <Send className="w-5 h-5" />
            </button>
          </form>
        </div>
      </div>
    );
  };

  return (
    <div className="max-w-md mx-auto">
      {currentPage === 'qrconnect' && <QRConnectPage />}
      {currentPage === 'connected' && <ConnectedPage />}
      {currentPage === 'translate' && <TranslatePage />}
    </div>
  );
};

export default App;
