# YouTube Transcript MCP Server

## Overview
The YouTube Transcript MCP server is a specialized microservice that provides access to YouTube video transcripts and transcript metadata. It leverages the `youtube_transcript_api` to fetch transcripts from YouTube videos and exposes them through a simple MCP interface.

## Features
- Extract transcripts from YouTube videos
- Support for multiple languages
- List available transcripts for a video
- Handles both YouTube URLs and video IDs
- Built on FastMCP framework

## Endpoints

### 1. Get Transcript
```
transcripts://{video_id}/{language}
```
Retrieves the transcript for a specific YouTube video in the requested language.

**Parameters:**
- `video_id`: YouTube video ID or full YouTube URL
- `language`: Language code (default: "en")

**Returns:**
- Raw transcript text with each line concatenated

### 2. List Available Transcripts
```
transcripts://{video_id}
```
Lists all available transcripts for a specific YouTube video.

**Parameters:**
- `video_id`: YouTube video ID or full YouTube URL

**Returns:**
- List of available transcripts with metadata including:
  - language: Full language name
  - language_code: ISO language code
  - is_generated: Whether the transcript was auto-generated
  - video_id: The video identifier

## Usage Examples

### Getting a Transcript
```python
# Using video ID
transcript = await mcp.get("transcripts://dQw4w9WgXcQ/en")

# Using full YouTube URL
transcript = await mcp.get("transcripts://https://www.youtube.com/watch?v=dQw4w9WgXcQ/en")
```

### Listing Available Transcripts
```python
# Get list of available transcripts
transcripts = await mcp.get("transcripts://dQw4w9WgXcQ")
```

## Error Handling
The server includes comprehensive error handling and logging:
- Invalid video IDs or URLs
- Unavailable transcripts
- Language not found
- API errors

## Dependencies
- youtube_transcript_api
- mcp (FastMCP framework)
- python-dotenv

## Environment Setup
The server uses environment variables loaded through python-dotenv. Ensure your `.env` file contains any necessary configuration.

## Logging
The server implements detailed logging with DEBUG level enabled by default, providing information about:
- Server startup
- Python path configuration
- MCP module location
- Error tracing for transcript operations
