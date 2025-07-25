# DWPP - WhatsApp SDK Client

A powerful and easy-to-use WhatsApp SDK client that abstracts the WhatsApp Cloud API for seamless integration into your projects.

## 🚀 Features

- [ ] **Read and Send WhatsApp Messages** - Full support for sending and receiving text messages
- [ ] **Webhook Server** - Built-in webhook server to handle incoming messages and events
- [ ] **Abstract WhatsApp Cloud API Layer** - Simplified interface that hides the complexity of the WhatsApp Cloud API
- [ ] **Media Support** - Handle images, videos, and audio files with ease

## 📋 Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Sending Messages](#sending-messages)
  - [Receiving Messages](#receiving-messages)
  - [Media Handling](#media-handling)
  - [Webhook Setup](#webhook-setup)
- [API Reference](#api-reference)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## 🛠 Installation

Add this to your `Cargo.toml`:

```toml
[dependencies]
dwpp = "0.1.0"
```

## 🚀 Quick Start

```rust
use dwpp::WhatsAppClient;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Initialize the client
    let client = WhatsAppClient::new("your_access_token", "your_phone_number_id");

    // Send a simple text message
    client.send_text_message("recipient_phone_number", "Hello from DWPP!").await?;

    Ok(())
}
```

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in your project root:

```env
WHATSAPP_ACCESS_TOKEN=your_access_token_here
WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id_here
WHATSAPP_WEBHOOK_VERIFY_TOKEN=your_webhook_verify_token_here
WEBHOOK_PORT=8080
```

### Manual Configuration

```rust
use dwpp::{WhatsAppClient, Config};

let config = Config::builder()
    .access_token("your_access_token")
    .phone_number_id("your_phone_number_id")
    .webhook_verify_token("your_webhook_verify_token")
    .webhook_port(8080)
    .build();

let client = WhatsAppClient::with_config(config);
```

## 📖 Usage

### Sending Messages

#### Text Messages
```rust
client.send_text_message("1234567890", "Hello World!").await?;
```

#### Template Messages
```rust
client.send_template_message("1234567890", "template_name", vec!["param1", "param2"]).await?;
```

### Receiving Messages

```rust
use dwpp::webhook::{WebhookServer, MessageEvent};

let mut webhook_server = WebhookServer::new(8080);

webhook_server.on_message(|event: MessageEvent| async move {
    println!("Received message: {}", event.text);
    // Process the message here
});

webhook_server.start().await?;
```

### Media Handling

#### Sending Images
```rust
client.send_image("1234567890", "path/to/image.jpg", Some("Image caption")).await?;
```

#### Sending Videos
```rust
client.send_video("1234567890", "path/to/video.mp4", Some("Video caption")).await?;
```

#### Sending Audio
```rust
client.send_audio("1234567890", "path/to/audio.mp3").await?;
```

#### Receiving Media
```rust
webhook_server.on_media(|event: MediaEvent| async move {
    let media_url = client.get_media_url(&event.media_id).await?;
    let media_data = client.download_media(&media_url).await?;

    // Process media data
    std::fs::write(format!("downloads/{}", event.filename), media_data)?;
});
```

### Webhook Setup

#### Basic Webhook Server
```rust
use dwpp::webhook::WebhookServer;

let webhook_server = WebhookServer::builder()
    .port(8080)
    .verify_token("your_verify_token")
    .build();

webhook_server.start().await?;
```

#### Custom Webhook Handlers
```rust
webhook_server
    .on_message(handle_text_message)
    .on_media(handle_media_message)
    .on_status(handle_status_update)
    .start()
    .await?;
```

## 📚 API Reference

### WhatsAppClient

| Method | Description | Parameters |
|--------|-------------|------------|
| `new()` | Create a new client instance | `access_token: &str, phone_number_id: &str` |
| `send_text_message()` | Send a text message | `to: &str, text: &str` |
| `send_image()` | Send an image | `to: &str, image_path: &str, caption: Option<&str>` |
| `send_video()` | Send a video | `to: &str, video_path: &str, caption: Option<&str>` |
| `send_audio()` | Send an audio file | `to: &str, audio_path: &str` |
| `get_media_url()` | Get media download URL | `media_id: &str` |
| `download_media()` | Download media content | `media_url: &str` |

### WebhookServer

| Method | Description | Parameters |
|--------|-------------|------------|
| `new()` | Create a new webhook server | `port: u16` |
| `on_message()` | Set message handler | `handler: impl Fn(MessageEvent)` |
| `on_media()` | Set media handler | `handler: impl Fn(MediaEvent)` |
| `on_status()` | Set status handler | `handler: impl Fn(StatusEvent)` |
| `start()` | Start the webhook server | None |

## 🔧 Examples

Check out the [examples](examples/) directory for more comprehensive usage examples:

- [Basic messaging](examples/basic_messaging.rs)
- [Media handling](examples/media_handling.rs)
- [Webhook server](examples/webhook_server.rs)
- [Template messages](examples/template_messages.rs)

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

- 📖 [Documentation](https://docs.rs/dwpp)
- 🐛 [Issue Tracker](https://github.com/your-username/dwpp/issues)
- 💬 [Discussions](https://github.com/your-username/dwpp/discussions)

## 🙏 Acknowledgments

- WhatsApp Cloud API team for providing the underlying API
- The Rust community for excellent crates and tools
- All contributors who help make this project better

---

**Note**: This SDK is not officially affiliated with WhatsApp or Meta. It's a community-driven project that provides a convenient interface to the WhatsApp Cloud API.
