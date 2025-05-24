# DiceBear Avatar Generator (PrimeVue + TailwindCSS v4)

点击切换中文版文档: [README-ZH.md](README-ZH.md)

## Project Overview

This is an interactive avatar generator based on the DiceBear API, integrated with PrimeVue and TailwindCSS v4. The
project allows users to select different avatar styles, customize colors and various attributes, preview and generate
usable avatars in real-time. Background colors dynamically change with the avatar colors, providing a harmonious visual
experience.

## Features

- Support for over 30 DiceBear avatar styles
- Real-time avatar preview and generation
- Dynamic background color adjustment
- Highly customizable avatar parameters
    - Background color selection
    - Color picker support
    - Various avatar attribute adjustments
- Responsive design
- Generation of shareable avatar URLs

## Tech Stack

- [Vue.js](https://vuejs.org/) - Progressive JavaScript framework
- [PrimeVue](https://primevue.org/) - Vue UI component library
- [TailwindCSS v4](https://tailwindcss.com/) - Utility-first CSS framework
- [DiceBear](https://dicebear.com/) - Open-source avatar generation API
- [Vite](https://vitejs.dev/) - Next generation frontend tooling

## Component Structure

The project primarily consists of the following components:

- `App.vue` - Root component, manages application state and background color
- `AvatarSelector.vue` - Main component for avatar selection and configuration
    - Supports style selection
    - Background color selection
    - Custom attributes for specific styles

## Getting Started

1. Clone the repository:

```bash
git clone https://github.com/yourusername/dicebear-avatar-generator.git
cd dicebear-avatar-generator
```

2. Install dependencies:

```bash
npm install
```

3. Start the development server:

```bash
npm run dev
```

4. Build for production:

```bash
npm run build
```

## Usage Guide

1. Select an avatar style in the Styles tab
2. Customize the background using the Background Color tab
3. Adjust specific attributes in other tabs according to the selected style
4. Click the generate button to get a shareable URL

## Dependencies

- @dicebear/collection - DiceBear avatar collections
- @dicebear/core - DiceBear core library
- PrimeVue components (Tabs, Button, ColorPicker, Slider, etc.)
- TailwindCSS v4 for styling

## Contributing

Issues and feature requests are welcome! For contributions, please open an issue first to discuss what you would like to
change.

## License

[MIT License](LICENSE)
