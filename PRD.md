'use client';

import React, { useState, useRef, useEffect } from 'react';

export default function StoryCraftPlayground() {
  const [messages, setMessages] = useState([
    { role: 'ai', text: '안녕, 시온아! 오늘은 어떤 이야기를 만들어볼까?' }
  ]);
  const [input, setInput] = useState('');
  const [isTyping, setIsTyping] = useState(false);
  const [imageUrl, setImageUrl] = useState(''); // 초기 빈 캔버스
  const messagesEndRef = useRef<HTMLDivElement>(null);

  // 자동 스크롤
  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  const handleSend = () => {
    if (!input.trim()) return;

    // 1. 사용자 메시지 추가
    const newMessages = [...messages, { role: 'user', text: input }];
    setMessages(newMessages);
    setInput('');
    setIsTyping(true);

    // 2. AI 응답 및 이미지 업데이트 (API 연동 전 가짜 지연 효과)
    setTimeout(() => {
      setMessages((prev) => [
        ...prev,
        { role: 'ai', text: '그때, 꿀꿀거리는 소리와 함께 무언가 나타났어요.' }
      ]);
      // 이미지 API 호출 결과라고 가정 (플레이스홀더 이미지)
      setImageUrl('https://via.placeholder.com/800x800/e2e8f0/64748b?text=Image+Generated'); 
      setIsTyping(false);
    }, 1500);
  };

  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key === 'Enter' && !e.nativeEvent.isComposing) {
      handleSend();
    }
  };

  return (
    <div className="flex flex-col md:flex-row h-screen bg-gray-50 text-gray-800 font-sans">
      
      {/* 1. 이미지 뷰어 영역 (좌측/상단) */}
      <div className="flex-1 p-6 md:p-10 flex flex-col items-center justify-center border-b md:border-b-0 md:border-r border-gray-200 bg-white shadow-sm z-10">
        <div className="w-full flex justify-between items-center mb-6 max-w-2xl">
          <h1 className="text-2xl font-bold text-gray-700">플레이그라운드</h1>
          <button className="text-sm px-4 py-2 bg-gray-100 hover:bg-gray-200 rounded-full transition">
            동화책 저장하기
          </button>
        </div>

        <div className="w-full max-w-2xl aspect-square bg-gray-100 rounded-2xl overflow-hidden shadow-inner flex items-center justify-center relative transition-all duration-500 ease-in-out">
          {imageUrl ? (
            <img 
              src={imageUrl} 
              alt="동화책 삽화" 
              className="object-cover w-full h-full animate-fade-in"
            />
          ) : (
            <div className="text-gray-400 flex flex-col items-center">
              <svg className="w-16 h-16 mb-4 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1.5} d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z" />
              </svg>
              <p>이야기를 시작하면 그림이 그려집니다</p>
            </div>
          )}
        </div>
      </div>

      {/* 2. 대화창 영역 (우측/하단) */}
      <div className="w-full md:w-[400px] lg:w-[480px] h-[50vh] md:h-full flex flex-col bg-white">
        
        {/* 대화 목록 */}
        <div className="flex-1 overflow-y-auto p-6 space-y-4">
          {messages.map((msg, idx) => (
            <div 
              key={idx} 
              className={`flex flex-col ${msg.role === 'user' ? 'items-end' : 'items-start'}`}
            >
              <span className="text-xs text-gray-400 mb-1 px-1">
                {msg.role === 'user' ? '시온이' : '동화 요정'}
              </span>
              <div 
                className={`max-w-[85%] px-4 py-3 rounded-2xl ${
                  msg.role === 'user' 
                    ? 'bg-blue-500 text-white rounded-tr-none' 
                    : 'bg-gray-100 text-gray-800 rounded-tl-none'
                }`}
              >
                {msg.text}
              </div>
            </div>
          ))}
          {isTyping && (
            <div className="flex items-start">
              <div className="bg-gray-100 text-gray-500 px-4 py-3 rounded-2xl rounded-tl-none animate-pulse">
                그림을 그리고 있어요...
              </div>
            </div>
          )}
          <div ref={messagesEndRef} />
        </div>

        {/* 입력창 */}
        <div className="p-4 border-t border-gray-100 bg-white">
          <div className="flex relative">
            <input
              type="text"
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyDown={handleKeyDown}
              placeholder="다음 이야기를 적어주세요..."
              className="w-full pl-5 pr-14 py-4 bg-gray-50 border border-gray-200 rounded-full focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow"
              disabled={isTyping}
            />
            <button
              onClick={handleSend}
              disabled={!input.trim() || isTyping}
              className="absolute right-2 top-2 bottom-2 aspect-square bg-blue-500 hover:bg-blue-600 disabled:bg-gray-300 text-white rounded-full flex items-center justify-center transition-colors"
            >
              <svg className="w-5 h-5 ml-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" />
              </svg>
            </button>
          </div>
        </div>

      </div>
    </div>
  );
}
