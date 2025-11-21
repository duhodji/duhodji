<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bo Bobzi - Fiction Writer</title>
    <!-- Load Tailwind CSS --><script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom fonts: Montserrat (Default), Gaegu (Name/Title), Lekton (Bio) */
        @import url('https://fonts.googleapis.com/css2?family=Gaegu:wght@300;400;700&family=Lekton:ital,wght@0,400;0,700;1,400&family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');
        
        body {
            font-family: 'Montserrat', sans-serif; /* Everything else */
            /* Initial background color set here, but will be overwritten by JS on click */
            background-color: #4a4a4a; 
            margin: 0;
            padding: 0;
            transition: background-color 0.5s ease; /* Smooth transition for background */
        }

        /* Target Name and Title with Gaegu */
        #nameText, #titleText {
            font-family: 'Gaegu', cursive;
        }

        /* Target Bio with Lekton */
        #bioText {
            font-family: 'Lekton', monospace;
        }

        /* Ensure the body takes up at least the height of the viewport */
        .full-center-container {
            min-height: 100vh;
        }
    </style>
</head>
<body id="pageBody">

    <!-- Header containing navigation links: Fixed to top right --><div id="headerNav" class="fixed top-4 right-4 z-10 flex space-x-4">
        
        <!-- Language Toggle Button --><button id="langToggle" class="text-sm font-medium text-gray-400 hover:text-gray-200 transition-colors duration-300 focus:outline-none">
            EN/pt
        </button>

        <!-- Contact Link (HREF is set by JS to pass the current theme and language) --><a id="contactLink" href="contact.html" class="text-sm font-medium text-gray-400 hover:text-gray-200 transition-colors duration-300">
            Contact
        </a>
    </div>

    <!-- Main content container: Uses flex utilities for perfect vertical and horizontal centering --><div class="full-center-container flex items-center justify-center p-4">

        <!-- Text and Image container. Layout is now vertical (flex-col) until 'md' breakpoint, where it switches to horizontal (md:flex-row). --><div id="centralContentWrapper" class="flex flex-col md:flex-row items-center justify-center space-y-8 md:space-y-0 md:space-x-12 relative cursor-pointer">
            
            <!-- AUTHOR IMAGE: Switched from SVG to IMG. Dimensions are controlled by Tailwind classes for responsiveness. --><div class="flex-shrink-0">
                <img id="placeholderIcon" alt="Author's Portrait" 
                     class="w-[150px] h-[150px] sm:w-[180px] sm:h-[180px] md:w-[200px] md:h-[200px] lg:w-[250px] lg:h-[250px] 
                            object-cover rounded-full  transition-all duration-500">
            </div>

            <!-- Text container (Made relative to position the bio absolutely beneath it) --><div id="centralTextContainer" class="text-center space-y-1 relative">
                
                <!-- The Name: Large and bold, initial text color is gray-100 (close to #f2f2f2) --><h1 id="nameText" class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-extrabold leading-none tracking-tight text-gray-100 rounded-lg transition-colors duration-500 cursor-default">
                    du hodji
                </h1>

                <!-- The Title/Role: Subtitle color is gray-400 --><p id="titleText" class="text-2xl sm:text-3xl md:text-4xl font-light text-gray-400  tracking-widest transition-colors duration-500 cursor-default">
                    Fiction Writer
                </p>

                <!-- The Bio Text (Appears on hover) --><!-- Positioned absolutely, centered horizontally, and initially hidden (opacity-0) --><div id="bioText" class="absolute left-1/2 transform -translate-x-1/2 mt-12 w-full max-w-lg md:max-w-xl 
                                        text-sm font-normal 
                                        opacity-0 transition-opacity duration-300 pointer-events-none z-20">
                    <!-- Content set by JS --></div>

            </div>
        </div>
    </div>

    <script>
        // Start of IIFE (Immediately Invoked Function Expression) to prevent variable conflicts
        (function() {

            // ==========================================================
            // === IMAGE URL CONFIGURATION (Paste your links here) ===
            // ==========================================================
            const IMAGE_URLS = {
                // This image will be used when the background is DARK (default)
                dark: 'duhodji_darkbg.png', 
                
                // This image will be used when the background is LIGHT
                light: 'duhodji_lightbg.png' 
            };
            // ==========================================================
            // ==========================================================

            // Function to extract a query parameter from the URL
            function getQueryParam(param) {
                const urlParams = new URLSearchParams(window.location.search);
                return urlParams.get(param);
            }
            
            // Check URL for theme parameter. Defaults to dark (isDark = true)
            const themeParam = getQueryParam('theme');
            let isDark = !(themeParam && themeParam.toLowerCase() === 'light');

            // NEW: Check URL for language parameter, defaults to English
            const langParam = getQueryParam('lang');
            let isEnglish = !(langParam && langParam.toLowerCase() === 'pt');


            // --- Color Toggle Logic Variables ---
            const THEMES = {
                dark: {
                    bg: '#4a4a4a',
                    h1: 'text-gray-100', // Represents #f2f2f2
                    p: 'text-gray-400',
                    link: 'text-gray-400 hover:text-gray-200',
                    bioText: 'text-gray-200', // Light text for dark background
                    // iconFill property is no longer used, but kept for context if needed
                    iconFill: '#888888' 
                },
                light: {
                    bg: '#f2f2f2', // New background
                    h1: 'text-gray-800', // Darker text for light background
                    p: 'text-gray-600', // Darker subtitle text
                    link: 'text-gray-700 hover:text-gray-900',
                    bioText: 'text-gray-700', // Dark text for light background
                    iconFill: '#cccccc' 
                }
            };
            const body = document.getElementById('pageBody');
            const h1 = document.getElementById('nameText');
            const bioText = document.getElementById('bioText');
            const placeholderIcon = document.getElementById('placeholderIcon'); // Now an <img>
            const centralContentWrapper = document.getElementById('centralContentWrapper'); // Reference to the entire central block
            
            // --- Language Toggle Logic Variables ---
            const titleText = document.getElementById('titleText');
            const langToggle = document.getElementById('langToggle');
            const contactLink = document.getElementById('contactLink');

            const LANGUAGE_MAP = {
                english: {
                    linkText: 'EN/pt', 
                    subtitle: 'fiction writer',
                    docTitle: 'du hodji - fiction writer',
                    contactText: 'contact',
                    bio: 'du hodji is a Brazilian writer currently living in New York. He writes short stories and flash fiction in English and Portuguese.'
                },
                portuguese: {
                    linkText: 'en/PT', 
                    subtitle: 'ficcionista',
                    docTitle: 'du hodji - ficcionista',
                    contactText: 'contato',
                    bio: 'du hodji é um escritor brasileiro vivendo atualmente em Nova York. Ele escreve contos e crônicas em portugês e inglês.'
                }
            };


            // --- Functions ---

            // Function to apply the color theme classes and styles
            function applyTheme(isDarkTheme) {
                const currentTheme = isDarkTheme ? THEMES.dark : THEMES.light;
                const oppositeTheme = isDarkTheme ? THEMES.light : THEMES.dark;
                const themeQuery = isDarkTheme ? 'dark' : 'light';
                const langQuery = isEnglish ? 'en' : 'pt';

                // 1. Update body background color directly
                body.style.backgroundColor = currentTheme.bg;

                // 2. Update H1 and P classes
                [h1, titleText].forEach(el => {
                    // Remove all theme-specific text color classes from both themes
                    el.classList.remove(THEMES.dark.h1, THEMES.light.h1, THEMES.dark.p, THEMES.light.p);
                });
                h1.classList.add(currentTheme.h1);
                titleText.classList.add(currentTheme.p);
                
                // 3. Update Header Link Colors
                [langToggle, contactLink].forEach(el => {
                    // Remove all theme-specific link color classes from both themes
                    el.classList.remove(...THEMES.dark.link.split(' '), ...THEMES.light.link.split(' '));
                    el.classList.add(...currentTheme.link.split(' '));
                });

                // 4. Update Bio Text Color
                bioText.classList.remove(oppositeTheme.bioText);
                bioText.classList.add(currentTheme.bioText);

                // 5. Update Placeholder Image Source (NEW)
                const currentImageUrl = isDarkTheme ? IMAGE_URLS.dark : IMAGE_URLS.light;
                placeholderIcon.src = currentImageUrl;
                
                // 6. Set Contact Link HREF to pass the current theme AND language
                contactLink.href = `contact.html?theme=${themeQuery}&lang=${langQuery}`;
            }

            // Function to update the text based on language state
            function updateLanguage(toEnglish) {
                const lang = toEnglish ? LANGUAGE_MAP.english : LANGUAGE_MAP.portuguese;
                document.title = lang.docTitle; // Update the browser tab title
                langToggle.textContent = lang.linkText;
                titleText.textContent = lang.subtitle;
                contactLink.textContent = lang.contactText; // Update the contact link text
                bioText.textContent = lang.bio; // Update the bio text
                
                // Re-apply theme to ensure the contact link URL is updated with the new language
                applyTheme(isDark);
            }
            
            // Functions to handle bio visibility on hover
            function showBio() {
                // Re-apply theme colors to bio box just in case the color toggle happened recently
                applyTheme(isDark); 
                bioText.classList.remove('opacity-0');
                bioText.classList.add('opacity-100');
            }

            function hideBio() {
                bioText.classList.remove('opacity-100');
                bioText.classList.add('opacity-0');
            }


            // --- Event Listeners ---

            // 1. Language Toggle Listener
            langToggle.addEventListener('click', (event) => {
                event.stopPropagation(); 
                isEnglish = !isEnglish;
                updateLanguage(isEnglish);
            });
            
            // 2. Color Toggle Listener (on document click)
            document.addEventListener('click', (event) => {
                // Check if the click occurred on the language toggle or contact link and skip color toggle
                if (event.target === langToggle || event.target === contactLink) {
                    return;
                }
                isDark = !isDark;
                applyTheme(isDark);
            });

            // 3. Bio Toggle Listeners (on hover over the entire central block)
            centralContentWrapper.addEventListener('mouseover', showBio);
            centralContentWrapper.addEventListener('mouseout', hideBio);


            // --- Initialization ---
            updateLanguage(isEnglish);
            // applyTheme is called inside updateLanguage, but calling it here ensures initial link setting if no language parameter exists
            applyTheme(isDark); 
            
        })(); // End of IIFE

    </script>

</body>
</html>
