# ai-prompt-box

Drop-in ChatGPT-style prompt input box for shadcn + Tailwind + TypeScript projects.

Features: autosizing textarea, image upload (click / drag / paste), image preview dialog, voice-recording UI with visualizer, and toggleable Search / Think / Canvas modes.

## Files

- `components/ui/ai-prompt-box.tsx` — the component (exports `PromptInputBox`)
- `components/ui/demo.tsx` — usage example on a radial-gradient background

## Install

```bash
npm install lucide-react framer-motion @radix-ui/react-dialog @radix-ui/react-tooltip
```

The project must already have:

- React 18+
- Tailwind CSS (with `tailwindcss-animate` plugin for the `animate-in` / `data-[state=…]` classes)
- TypeScript
- shadcn-style alias `@/*` → project root (so `@/components/ui/ai-prompt-box` resolves)

If you don't have shadcn set up:

```bash
npx shadcn@latest init
```

This creates `components.json`, the `@/components/ui` path, and the `cn` helper convention. The component above ships its own inline `cn`, so it works even without `@/lib/utils`.

## Why `/components/ui`?

shadcn's convention puts copy-pasted primitives in `components/ui/` so they're easy to find, override, and keep separate from app-specific composite components. Sticking to this path means imports like `@/components/ui/ai-prompt-box` work out of the box and match the rest of the shadcn ecosystem.

## Usage

```tsx
import { PromptInputBox } from "@/components/ui/ai-prompt-box";

export default function Page() {
  return (
    <PromptInputBox
      onSend={(message, files) => {
        // hand off to your chat API
        console.log(message, files);
      }}
      isLoading={false}
      placeholder="Ask anything…"
    />
  );
}
```

### Props

| Prop          | Type                                          | Default                          |
| ------------- | --------------------------------------------- | -------------------------------- |
| `onSend`      | `(message: string, files?: File[]) => void`   | no-op                            |
| `isLoading`   | `boolean`                                     | `false`                          |
| `placeholder` | `string`                                      | `"Type your message here..."`    |
| `className`   | `string`                                      | —                                |

When a mode toggle is on, `message` is wrapped: `"[Search: …]"`, `"[Think: …]"`, `"[Canvas: …]"`. Voice messages send as `"[Voice message - N seconds]"`.

## Notes

- The component runs `document.createElement("style")` at module load, so it's **client-only**. In Next.js App Router, mark the importing file with `"use client"`.
- Voice recording is UI-only (no `MediaRecorder` wiring) — replace `handleStartRecording` / `handleStopRecording` with real capture if you need audio data.
- Image upload caps at 10MB and accepts a single image at a time.
