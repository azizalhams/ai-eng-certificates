import os
from azure.ai.contentunderstanding import ContentUnderstandingClient
from azure.core.credentials import AzureKeyCredential

# Endpoint and key for your Foundry resource
endpoint = os.environ["FOUNDRY_ENDPOINT"]  # e.g., "https://<resource>.services.ai.azure.com/"
key = os.environ["FOUNDRY_KEY"]

client = ContentUnderstandingClient(
    endpoint=endpoint,
    credential=AzureKeyCredential(key)
)

# Choose a prebuilt analyzer for audio
# (The documents module lists examples like prebuilt-audioSearch / prebuilt-videoSearch.)
analyzer_id = "prebuilt-audioSearch"

# Provide an input audio file (URL shown here; you can swap in your own accessible media URL)
inputs = [
    {"url": "https://<your-host>/samples/voicemail.wav"}
]

# Start analysis (asynchronous long-running operation)
poller = client.begin_analyze(analyzer_id=analyzer_id, inputs=inputs)

# Wait for completion (SDK polls under the hood)
result = poller.result()

# Inspect the structured output (JSON-like objects)
for content in result.contents:
    # Some analyzers may return a transcript and/or extracted fields depending on the analyzer and schema
    print("=== MARKDOWN / TRANSCRIPT (if provided) ===")
    print(getattr(content, "markdown", None))

    print("\n=== EXTRACTED FIELDS ===")
    print(getattr(content, "fields", None))