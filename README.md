<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Tram Ticket Keeper</title>
    <meta name="description" content="Track your tram subscription with auto-renewal" />
    <meta name="author" content="Lovable" />

    <meta property="og:title" content="Tram Ticket Keeper" />
    <meta property="og:description" content="Track your tram subscription with auto-renewal" />
    <meta property="og:type" content="website" />
    <meta property="og:image" content="https://lovable.dev/opengraph-image-p98pqg.png" />

    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:site" content="@lovable_dev" />
    <meta name="twitter:image" content="https://lovable.dev/opengraph-image-p98pqg.png" />
  </head>

  <body>
    <div id="root"></div>
    <!-- IMPORTANT: DO NOT REMOVE THIS SCRIPT TAG OR THIS VERY COMMENT! -->
    <script src="https://cdn.gpteng.co/gptengineer.js" type="module"></script>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
import { Toaster } from "@/components/ui/toaster";
import { Toaster as Sonner } from "@/components/ui/sonner";
import { TooltipProvider } from "@/components/ui/tooltip";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Index from "./pages/Index";
import NotFound from "./pages/NotFound";

const queryClient = new QueryClient();

const App = () => (
  <QueryClientProvider client={queryClient}>
    <TooltipProvider>
      <Toaster />
      <Sonner />
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Index />} />
          <Route path="*" element={<NotFound />} />
        </Routes>
      </BrowserRouter>
    </TooltipProvider>
  </QueryClientProvider>
);

export default App;
import React, { useState, useEffect } from 'react';

interface CountdownTimerProps {
  expiryDate: Date;
  onExpire?: () => void;
}

const CountdownTimer: React.FC<CountdownTimerProps> = ({ expiryDate, onExpire }) => {
  const [timeLeft, setTimeLeft] = useState({
    days: 0,
    hours: 0,
    minutes: 0,
    seconds: 0
  });

  useEffect(() => {
    const calculateTimeLeft = () => {
      const difference = expiryDate.getTime() - new Date().getTime();
      
      if (difference <= 0) {
        // Ticket has expired
        if (onExpire) onExpire();
        return { days: 0, hours: 0, minutes: 0, seconds: 0 };
      }
      
      // Calculate the time units
      const days = Math.floor(difference / (1000 * 60 * 60 * 24));
      const hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
      const minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
      const seconds = Math.floor((difference % (1000 * 60)) / 1000);
      
      return { days, hours, minutes, seconds };
    };

    // Calculate initially
    setTimeLeft(calculateTimeLeft());
    
    // Set up interval to update every second
    const timer = setInterval(() => {
      const newTimeLeft = calculateTimeLeft();
      setTimeLeft(newTimeLeft);
      
      // If the countdown is finished, clear the interval
      if (newTimeLeft.days === 0 && newTimeLeft.hours === 0 && 
          newTimeLeft.minutes === 0 && newTimeLeft.seconds === 0) {
        clearInterval(timer);
      }
    }, 1000);
    
    // Clear interval on unmount
    return () => clearInterval(timer);
  }, [expiryDate, onExpire]);

  // Format numbers to always show 2 digits
  const formatNumber = (num: number) => String(num).padStart(2, '0');

  return (
    <div className="w-full bg-tramGreen text-white grid grid-cols-4 text-center py-2">
      <div className="flex flex-col">
        <span className="text-2xl font-bold">{timeLeft.days}</span>
        <span className="text-sm">days</span>
      </div>
      <div className="flex flex-col">
        <span className="text-2xl font-bold">{formatNumber(timeLeft.hours)}</span>
        <span className="text-sm">hrs</span>
      </div>
      <div className="flex flex-col">
        <span className="text-2xl font-bold">{formatNumber(timeLeft.minutes)}</span>
        <span className="text-sm">mins</span>
      </div>
      <div className="flex flex-col">
        <span className="text-2xl font-bold">{formatNumber(timeLeft.seconds)}</span>
        <span className="text-sm">secs</span>
      </div>
    </div>
  );
};

export default CountdownTimer;
import React from 'react';
import { Menu } from 'lucide-react';

const Header: React.FC = () => {
  return (
    <div className="flex justify-between items-center p-4">
      <Menu className="h-6 w-6 text-gray-500" />
      <h1 className="text-center text-xl font-medium text-gray-700">My Tickets</h1>
      <div className="h-6 w-6 rounded-full bg-tramYellow"></div>
    </div>
  );
};

export default Header;
import React from 'react';

interface QRCodeProps {
  value: string;
  size?: number;
}

const QRCode: React.FC<QRCodeProps> = ({ value, size = 120 }) => {
  // In a real implementation, this would generate an actual QR code
  // For now, we'll use a placeholder image that looks like a QR code
  return (
    <div 
      className="bg-white p-2 inline-block"
      style={{ width: size, height: size }}
    >
      <div className="grid grid-cols-5 grid-rows-5 gap-1 w-full h-full">
        {/* This creates a basic QR code pattern */}
        {Array.from({ length: 25 }).map((_, i) => (
          <div 
            key={i} 
            className={`${Math.random() > 0.7 ? 'bg-black' : 'bg-white'} ${
              // Add fixed patterns to make it look more like a QR code
              (i === 0 || i === 4 || i === 20 || i === 24) ? 'bg-black' : ''
            }`}
          />
        ))}
      </div>
    </div>
  );
};

export default QRCode;
import React from 'react';

const TramIcon: React.FC = () => {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="100%"
      height="100%"
      viewBox="0 0 240 60"
      fill="none"
      className="max-w-xs"
    >
      <path
        d="M20,40 L50,40 C60,40 60,20 50,20 L20,20 C10,20 10,40 20,40 Z"
        fill="#4CAF50"
        stroke="white"
        strokeWidth="2"
      />
      <path
        d="M70,40 L100,40 C110,40 110,20 100,20 L70,20 C60,20 60,40 70,40 Z"
        fill="#4CAF50"
        stroke="white"
        strokeWidth="2"
      />
      <path
        d="M120,40 L150,40 C160,40 160,20 150,20 L120,20 C110,20 110,40 120,40 Z"
        fill="#4CAF50"
        stroke="white"
        strokeWidth="2"
      />
      <path
        d="M170,40 L200,40 C210,40 210,20 200,20 L170,20 C160,20 160,40 170,40 Z"
        fill="#4CAF50"
        stroke="white"
        strokeWidth="2"
      />
      <path
        d="M10,40 L220,40"
        stroke="white"
        strokeWidth="2"
      />
      <circle cx="30" cy="45" r="5" fill="black" />
      <circle cx="80" cy="45" r="5" fill="black" />
      <circle cx="130" cy="45" r="5" fill="black" />
      <circle cx="180" cy="45" r="5" fill="black" />
    </svg>
  );
};

export default TramIcon;
import React from 'react';
import { format } from 'date-fns';
import { Calendar, MapPin, User } from 'lucide-react';
import QRCode from './QRCode';
import CountdownTimer from './CountdownTimer';

interface TramTicketProps {
  ticketType: string;
  zones: string;
  activeTickets: string;
  currentTimestamp: string;
  createdDate: Date;
  validUntilDate: Date;
  additionalInfo?: string;
  onExpire?: () => void;
}

const TramTicket: React.FC<TramTicketProps> = ({
  ticketType,
  zones,
  activeTickets,
  currentTimestamp,
  createdDate,
  validUntilDate,
  additionalInfo = "Multiple & Group ticket passengers must travel together",
  onExpire
}) => {
  return (
    <div className="relative mx-auto max-w-md w-full bg-white shadow-lg rounded-lg overflow-hidden border border-gray-200">
      {/* Ticket Header - Green bar with ticket type */}
      <div className="bg-tramGreen text-white p-3 flex items-center justify-between">
        <div className="flex items-center">
          <div className="bg-white text-tramGreen px-2 py-1 rounded-full font-bold text-xs flex items-center">
            NET<span className="text-tramYellow font-bold">GO!</span>
          </div>
        </div>
        <div className="flex items-center text-white gap-2">
          <Calendar className="h-5 w-5 text-tramYellow" />
          <span className="text-xl font-bold">{ticketType}</span>
        </div>
      </div>

      {/* Zones and Active Tickets Section */}
      <div className="bg-tramPink p-3 flex justify-between items-center">
        <div className="flex items-center">
          <MapPin className="h-4 w-4 mr-1 text-gray-600" />
          <span className="text-sm font-semibold text-gray-800">{zones}</span>
        </div>
        <div className="flex items-center">
          <span className="text-sm text-gray-800 mr-2">Active Tickets:</span>
          <div className="h-8 w-8 rounded-full border-2 border-tramYellow flex items-center justify-center text-sm">
            {activeTickets}
          </div>
        </div>
      </div>
      
      {/* User icon and QR code section */}
      <div className="p-4 flex justify-between items-center">
        <div className="flex items-center justify-center">
          <User className="h-20 w-20 text-tramYellow" />
        </div>
        <div>
          <QRCode value="https://example.com/ticket" />
        </div>
      </div>
      
      {/* ID Required badge */}
      <div className="flex justify-center -mt-2 mb-4">
        <div className="bg-tramPink px-4 py-1 rounded-lg flex items-center">
          <div className="mr-2 bg-white p-1 rounded">
            <User className="h-4 w-4 text-gray-600" />
          </div>
          <span className="text-sm text-gray-700">ID Required</span>
        </div>
      </div>
      
      {/* Current timestamp */}
      <div className="text-center mb-4 font-mono">
        <p className="text-lg">{currentTimestamp}</p>
      </div>
      
      {/* Created date section */}
      <div className="bg-tramGreen text-white p-3 rounded-t-lg">
        <p className="text-center text-sm">Created:</p>
        <p className="text-center text-2xl font-bold">
          {format(createdDate, 'EEE dd MMM yyyy HH:mm')}
        </p>
      </div>
      
      {/* Valid until section */}
      <div className="bg-tramGreen text-white px-3 pb-3">
        <p className="text-center text-sm">Valid until:</p>
        <div className="bg-tramLightGreen rounded-lg p-2 mb-2">
          <p className="text-center text-2xl font-bold">
            {format(validUntilDate, 'EEE dd MMM yyyy HH:mm')}
          </p>
        </div>
        
        {/* Additional info */}
        <p className="text-center text-sm pb-3">{additionalInfo}</p>
      </div>
      
      {/* Train icon */}
      <div className="flex justify-center bg-white py-4">
        <img src="/lovable-uploads/9dd474a1-e190-4d2a-8d82-adce99f60b99.png" alt="Tram" className="h-6" />
      </div>
      
      {/* Countdown timer */}
      <CountdownTimer expiryDate={validUntilDate} onExpire={onExpire} />
    </div>
  );
};

export default TramTicket;
import React, { useState, useEffect } from 'react';
import { toast } from '@/components/ui/sonner';
import { generateNewTicket, renewTicket, formatCurrentTimestamp, shouldRenewTicket, Ticket } from '@/utils/ticketUtils';
import TramTicket from '@/components/TramTicket';
import Header from '@/components/Header';
import TramIcon from '@/components/TramIcon';

const Index = () => {
  // State for the active ticket
  const [ticket, setTicket] = useState<Ticket | null>(null);
  // State for the current timestamp (updates every second)
  const [currentTimestamp, setCurrentTimestamp] = useState(formatCurrentTimestamp());

  // Generate a new ticket when the component mounts
  useEffect(() => {
    // Check if there's a saved ticket in localStorage
    const savedTicket = localStorage.getItem('tramTicket');
    
    if (savedTicket) {
      const parsedTicket = JSON.parse(savedTicket);
      // Convert string dates back to Date objects
      parsedTicket.createdDate = new Date(parsedTicket.createdDate);
      parsedTicket.validUntilDate = new Date(parsedTicket.validUntilDate);
      
      // Check if the ticket should be renewed
      if (shouldRenewTicket(parsedTicket)) {
        const renewedTicket = renewTicket(parsedTicket);
        setTicket(renewedTicket);
        localStorage.setItem('tramTicket', JSON.stringify(renewedTicket));
        toast('Your ticket has been automatically renewed for another month!');
      } else {
        setTicket(parsedTicket);
      }
    } else {
      // No saved ticket, generate a new one
      const newTicket = generateNewTicket();
      setTicket(newTicket);
      localStorage.setItem('tramTicket', JSON.stringify(newTicket));
      toast('Your new tram pass has been activated!');
    }
    
    // Update the timestamp every second
    const intervalId = setInterval(() => {
      setCurrentTimestamp(formatCurrentTimestamp());
    }, 1000);
    
    return () => clearInterval(intervalId);
  }, []);

  // Handle ticket expiration
  const handleTicketExpire = () => {
    if (ticket && ticket.autoRenew) {
      const renewedTicket = renewTicket(ticket);
      setTicket(renewedTicket);
      localStorage.setItem('tramTicket', JSON.stringify(renewedTicket));
      toast('Your ticket has been automatically renewed for another month!');
    } else {
      toast('Your ticket has expired. Please purchase a new one.');
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 flex flex-col">
      <Header />
      
      <div className="flex-1 p-4">
        {ticket ? (
          <TramTicket
            ticketType={ticket.type}
            zones={ticket.zones}
            activeTickets="1/1"
            currentTimestamp={currentTimestamp}
            createdDate={ticket.createdDate}
            validUntilDate={ticket.validUntilDate}
            onExpire={handleTicketExpire}
          />
        ) : (
          <div className="flex justify-center items-center h-full">
            <p>Loading ticket...</p>
          </div>
        )}
      </div>
    </div>
  );
};

export default Index;
import { addDays, addMonths } from 'date-fns';

export interface Ticket {
  id: string;
  type: string;
  zones: string;
  createdDate: Date;
  validUntilDate: Date;
  autoRenew: boolean;
}

// Generate a 30-day ticket (1 month)
export const generateNewTicket = (autoRenew: boolean = true): Ticket => {
  const now = new Date();
  const validUntil = addMonths(now, 1);
  
  return {
    id: `ticket-${Date.now()}`,
    type: "1 Month Under 19",
    zones: "All Zones",
    createdDate: now,
    validUntilDate: validUntil,
    autoRenew
  };
};

// Renew a ticket for another 30 days
export const renewTicket = (ticket: Ticket): Ticket => {
  const now = new Date();
  const validUntil = addMonths(now, 1);
  
  return {
    ...ticket,
    id: `ticket-${Date.now()}`,
    createdDate: now,
    validUntilDate: validUntil
  };
};

// Format current timestamp as shown in the image
export const formatCurrentTimestamp = (): string => {
  const now = new Date();
  
  const day = String(now.getDate()).padStart(2, '0');
  const month = String(now.getMonth() + 1).padStart(2, '0');
  const year = now.getFullYear();
  
  const hours = String(now.getHours()).padStart(2, '0');
  const minutes = String(now.getMinutes()).padStart(2, '0');
  const seconds = String(now.getSeconds()).padStart(2, '0');
  
  const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
  const dayName = days[now.getDay()];
  
  return `${dayName} ${day} ${month} ${year} ${hours}:${minutes}:${seconds}`;
};

// Check if the ticket needs renewal
export const shouldRenewTicket = (ticket: Ticket): boolean => {
  return ticket.autoRenew && new Date() >= ticket.validUntilDate;
};
import type { Config } from "tailwindcss";

export default {
	darkMode: ["class"],
	content: [
		"./pages/**/*.{ts,tsx}",
		"./components/**/*.{ts,tsx}",
		"./app/**/*.{ts,tsx}",
		"./src/**/*.{ts,tsx}",
	],
	prefix: "",
	theme: {
		container: {
			center: true,
			padding: '2rem',
			screens: {
				'2xl': '1400px'
			}
		},
		extend: {
			colors: {
				border: 'hsl(var(--border))',
				input: 'hsl(var(--input))',
				ring: 'hsl(var(--ring))',
				background: 'hsl(var(--background))',
				foreground: 'hsl(var(--foreground))',
				primary: {
					DEFAULT: 'hsl(var(--primary))',
					foreground: 'hsl(var(--primary-foreground))'
				},
				secondary: {
					DEFAULT: 'hsl(var(--secondary))',
					foreground: 'hsl(var(--secondary-foreground))'
				},
				destructive: {
					DEFAULT: 'hsl(var(--destructive))',
					foreground: 'hsl(var(--destructive-foreground))'
				},
				muted: {
					DEFAULT: 'hsl(var(--muted))',
					foreground: 'hsl(var(--muted-foreground))'
				},
				accent: {
					DEFAULT: 'hsl(var(--accent))',
					foreground: 'hsl(var(--accent-foreground))'
				},
				popover: {
					DEFAULT: 'hsl(var(--popover))',
					foreground: 'hsl(var(--popover-foreground))'
				},
				card: {
					DEFAULT: 'hsl(var(--card))',
					foreground: 'hsl(var(--card-foreground))'
				},
				sidebar: {
					DEFAULT: 'hsl(var(--sidebar-background))',
					foreground: 'hsl(var(--sidebar-foreground))',
					primary: 'hsl(var(--sidebar-primary))',
					'primary-foreground': 'hsl(var(--sidebar-primary-foreground))',
					accent: 'hsl(var(--sidebar-accent))',
					'accent-foreground': 'hsl(var(--sidebar-accent-foreground))',
					border: 'hsl(var(--sidebar-border))',
					ring: 'hsl(var(--sidebar-ring))'
				},
				tramGreen: '#4CAF50',
				tramLightGreen: '#8BC34A', 
				tramYellow: '#D4CB40',
				tramPink: '#FFE4E1',
			},
			borderRadius: {
				lg: 'var(--radius)',
				md: 'calc(var(--radius) - 2px)',
				sm: 'calc(var(--radius) - 4px)'
			},
			keyframes: {
				'accordion-down': {
					from: {
						height: '0'
					},
					to: {
						height: 'var(--radix-accordion-content-height)'
					}
				},
				'accordion-up': {
					from: {
						height: 'var(--radix-accordion-content-height)'
					},
					to: {
						height: '0'
					}
				}
			},
			animation: {
				'accordion-down': 'accordion-down 0.2s ease-out',
				'accordion-up': 'accordion-up 0.2s ease-out'
			}
		}
	},
	plugins: [require("tailwindcss-animate")],
} satisfies Config;

