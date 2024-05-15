<script lang="ts">
    async function startRecordingTab() {
        try {
            // Requesting media stream of the current tab
            const mediaStream = await navigator.mediaDevices.getDisplayMedia({
                video: true,
                // @ts-ignore
                preferCurrentTab: true,
            });

            // Setting up the MediaRecorder
            const mediaRecorder = new MediaRecorder(mediaStream);
            let chunks: BlobPart[] = [];

            mediaRecorder.ondataavailable = (event) => {
                if (event.data.size > 0) {
                    chunks.push(event.data);
                }
            };

            mediaRecorder.onstop = () => {
                const blob = new Blob(chunks, { type: "video/webm" });
                downloadVideo(blob);
                mediaStream.getTracks().forEach((track) => track.stop()); // Stop the media stream
            };

            mediaRecorder.start(); // Start recording

            // Optional: Stop recording after a set time
            setTimeout(() => {
                mediaRecorder.stop(); // Stops recording
            }, 1000); // Adjust duration as needed, in milliseconds
        } catch (error) {
            console.error("Error capturing tab:", error);
        }
    }

    function downloadVideo(blob: Blob) {
        const url = URL.createObjectURL(blob);
        const anchor = document.createElement("a");
        anchor.href = url;
        anchor.download = "tab-capture.webm";
        anchor.click();
        URL.revokeObjectURL(url); // Clean up
    }

    // Call this function to start the recording
    startRecordingTab();
</script>

<main>
    <iframe
        style:width="80vw"
        style:height="80vh"
        src="https://introcomp.mit.edu/6.100L_fa23"
        allow="camera; microphone"
        title="web"
    />
</main>

<style>
    .logo {
        height: 6em;
        padding: 1.5em;
        will-change: filter;
        transition: filter 300ms;
    }
    .logo:hover {
        filter: drop-shadow(0 0 2em #646cffaa);
    }
    .logo.svelte:hover {
        filter: drop-shadow(0 0 2em #ff3e00aa);
    }
    .read-the-docs {
        color: #888;
    }
</style>
