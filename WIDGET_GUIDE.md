# OpenSesame Widget Integration Guide

Complete guide for integrating and using the OpenSesame embeddable widget in your application.

## Table of Contents

1. [Quick Start](#quick-start)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [API Methods](#api-methods)
5. [Event Callbacks](#event-callbacks)
6. [Error Handling](#error-handling)
7. [Examples](#examples)
8. [Troubleshooting](#troubleshooting)

---

## Quick Start

The fastest way to get started is with automatic configuration using your Cell ID:

```html
<script>
  window.OpenSesameCellConfig = {
    cellId: 'your-cell-id-here'
  };
</script>
<script src="https://cell.opensesame.dev/widget.js" async></script>
<link rel="stylesheet" href="https://cell.opensesame.dev/widget.css">
```

The widget will automatically:
- Fetch your cell configuration from the API
- Initialize with your branding and settings
- Handle authentication
- Be ready to use immediately

---

## Installation

### HTML

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="https://cell.opensesame.dev/widget.css">
</head>
<body>
  <script>
    window.OpenSesameCellConfig = {
      cellId: 'your-cell-id-here'
    };
  </script>
  <script src="https://cell.opensesame.dev/widget.js" async></script>
</body>
</html>
```

**Note:** The CSS link is optional - the widget will inject it automatically if not provided.

### Next.js

```javascript
// pages/_app.js or app/layout.js
import Script from 'next/script';

export default function App({ Component, pageProps }) {
  return (
    <>
      <Script id="opensesame-widget-config" strategy="beforeInteractive">
        {`
          window.OpenSesameCellConfig = {
            cellId: 'your-cell-id-here'
          };
        `}
      </Script>
      <Script 
        src="https://cell.opensesame.dev/widget.js" 
        strategy="lazyOnload"
      />
    </>
  );
}
```

### React Native

For React Native, you'll need to use a WebView component:

```javascript
import { WebView } from 'react-native-webview';

export default function App() {
  const htmlContent = `
    <!DOCTYPE html>
    <html>
    <head>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="https://cell.opensesame.dev/widget.css">
    </head>
    <body>
      <script>
        window.OpenSesameCellConfig = {
          cellId: 'your-cell-id-here'
        };
      </script>
      <script src="https://cell.opensesame.dev/widget.js" async></script>
    </body>
    </html>
  `;

  return (
    <WebView
      source={{ html: htmlContent }}
      style={{ flex: 1 }}
    />
  );
}
```

---

## Configuration

### Automatic Configuration (Recommended)

Using `cellId` is the simplest approach - the widget fetches all configuration automatically:

```javascript
window.OpenSesameCellConfig = {
  cellId: 'your-cell-id-here'
};
```

### Manual Configuration

For more control, you can provide configuration directly:

```javascript
window.OpenSesameCellConfig = {
  cellId: 'your-cell-id-here',
  primaryColor: '#5E17EB',
  secondaryColor: '#F3EEFD',
  borderRadius: 32,
  defaultWidth: 800,
  bottomMargin: 25,
  placeholderText: 'How can I help you?',
  suggestedQuestions: [
    'What are your hours?',
    'How do I contact support?',
    'What is your refund policy?'
  ],
  suggestedQuestionsBehavior: 'hover', // 'never' | 'always' | 'hover'
  collapsedStyle: 'bar', // 'bar' | 'circular'
  collapseIcon: 'x', // 'minus' | 'x'
  userId: 'user-123',
  auth: {
    'Authorization': 'Bearer your-token-here'
  }
};
```

### Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `cellId` | string | **Required** | Your unique cell identifier |
| `primaryColor` | string | `#5E17EB` | Primary brand color (hex) |
| `secondaryColor` | string | `#F3EEFD` | Secondary color (hex) |
| `accentColor` | string | `#F3EEFD` | Accent color (hex) |
| `textColor` | string | Auto | Text color (auto-calculated from primary) |
| `borderColor` | string | `null` | Border color (hex) |
| `glowColor` | string | `null` | Glow effect color (hex) |
| `borderWidth` | number | `0` | Border width in pixels |
| `borderRadius` | number | `32` | Border radius in pixels (max 32) |
| `defaultWidth` | number | `800` | Default width when expanded (px) |
| `bottomMargin` | number | `25` | Bottom margin in pixels |
| `placeholderText` | string | `'What can I do for you?'` | Input placeholder text |
| `suggestedQuestions` | string[] | `[]` | Array of suggested questions |
| `suggestedQuestionsBehavior` | string | `'never'` | When to show suggestions: `'never'`, `'always'`, `'hover'` |
| `collapsedStyle` | string | `'bar'` | Collapsed state style: `'bar'` or `'circular'` |
| `collapseIcon` | string | `'x'` | Collapse button icon: `'minus'` or `'x'` |
| `logo` | string | Auto | Logo URL (auto-selected based on primary color) |
| `userId` | string | Auto | User identifier (auto-derived from origin if not provided) |
| `auth` | object | `{}` | Authentication headers (key-value pairs) |
| `customHeaders` | object | `{}` | Custom HTTP headers |

---

## API Methods

### Widget Control

#### `show()`

Programmatically show/expand the widget.

```javascript
window.OpenSesameCellConfig.show();
```

#### `hide()`

Programmatically hide/collapse the widget.

```javascript
window.OpenSesameCellConfig.hide();
```

#### `destroy()`

Remove the widget from the page and clean up resources.

```javascript
window.OpenSesameCellConfig.destroy();
```

### User Identification

#### `identify(userData)`

Identify the current user for analytics and personalization.

```javascript
window.OpenSesameCellConfig.identify({
  user_id: 'user-123',
  email: 'user@example.com',
  name: 'John Doe',
  company: {
    id: 'company-456',
    name: 'Acme Corp'
  },
  custom_attributes: {
    plan: 'pro',
    signup_date: '2024-01-01',
    lifetime_value: 5000
  }
});
```

**Parameters:**
- `user_id` (string, optional): Unique user identifier
- `email` (string, optional): User email address
- `name` (string, optional): User's full name
- `company` (object, optional): Company information with `id` and `name`
- `custom_attributes` (object, optional): Any additional user metadata

**Note:** You can call `identify()` multiple times - new data will be merged with existing data.

#### `update(userData)`

Convenience method to update user metadata (alias for `identify()`).

```javascript
window.OpenSesameCellConfig.update({
  email: 'newemail@example.com',
  custom_attributes: {
    plan: 'enterprise'
  }
});
```

### Event Tracking

#### `trackEvent(eventName, properties)`

Track custom events for analytics.

```javascript
window.OpenSesameCellConfig.trackEvent('purchase-completed', {
  price: 99.99,
  currency: 'USD',
  product: 'Premium Plan',
  order_id: 'order-123'
});
```

**Parameters:**
- `eventName` (string, required): Event name
- `properties` (object, optional): Event properties/metadata

**Note:** User identification data is automatically included with tracked events.

#### `trackPageView(properties)`

Convenience method to track page views.

```javascript
window.OpenSesameCellConfig.trackPageView({
  title: document.title,
  referrer: document.referrer,
  path: window.location.pathname
});
```

**Parameters:**
- `properties` (object, optional): Additional page view properties

Automatically captures `url` from `window.location.href` if not provided.

### Configuration Updates

#### `updateConfig(newConfig)`

Update widget configuration at runtime (colors, size, styling, etc.).

```javascript
window.OpenSesameCellConfig.updateConfig({
  primaryColor: '#FF5733',
  borderRadius: 20,
  defaultWidth: 600,
  bottomMargin: 30,
  placeholderText: 'Ask me anything!'
});
```

**Parameters:**
- `newConfig` (object): Partial configuration object with properties to update

**Supported properties:**
- `primaryColor`, `secondaryColor`, `accentColor`, `textColor`
- `borderColor`, `glowColor`, `borderWidth`
- `borderRadius`, `defaultWidth`, `bottomMargin`
- `placeholderText`, `suggestedQuestions`, `suggestedQuestionsBehavior`
- `collapsedStyle`, `collapseIcon`, `logo`

### Widget State

#### `getState()`

Get current widget state for debugging and monitoring.

```javascript
const state = window.OpenSesameCellConfig.getState();
console.log(state);
// {
//   version: '1.0.0-1234567890',
//   ready: true,
//   hasError: false,
//   error: null,
//   cellId: 'your-cell-id',
//   userId: 'user-123',
//   userIdentified: true,
//   pendingEvents: 0,
//   timestamp: '2024-01-01T12:00:00.000Z'
// }
```

**Returns:**
- `version` (string): Widget version
- `ready` (boolean): Whether widget is ready
- `hasError` (boolean): Whether widget has an error
- `error` (object|null): Current error object (if any)
- `cellId` (string|null): Current cell ID
- `userId` (string|null): Current user ID
- `userIdentified` (boolean): Whether user has been identified
- `pendingEvents` (number): Number of queued events
- `timestamp` (string): Current timestamp (ISO format)

#### `version`

Get widget version string.

```javascript
const version = window.OpenSesameCellConfig.version;
console.log(version); // '1.0.0-1234567890'
```

### Error Handling

#### `hasError`

Check if widget has an error.

```javascript
if (window.OpenSesameCellConfig.hasError) {
  console.error('Widget has an error');
}
```

#### `getError()`

Get current error object.

```javascript
const error = window.OpenSesameCellConfig.getError();
if (error) {
  console.error('Error type:', error.type);
  console.error('Error message:', error.message);
  console.error('Error context:', error.context);
}
```

**Error object structure:**
- `type` (string): Error type (`'initialization'`, `'rendering'`, `'react_boundary'`, `'network'`)
- `message` (string): Error message
- `error` (Error): Original error object
- `context` (object): Additional error context
- `timestamp` (string): Error timestamp (ISO format)

#### `clearError()`

Clear current error state.

```javascript
window.OpenSesameCellConfig.clearError();
```

#### `retry()`

Retry widget initialization after an error.

```javascript
try {
  await window.OpenSesameCellConfig.retry();
  console.log('Widget retry successful');
} catch (error) {
  console.error('Widget retry failed:', error);
}
```

**Returns:** Promise that resolves when retry completes

---

## Event Callbacks

Set callbacks to react to widget lifecycle events and user interactions.

### `onReady`

Called when the widget is fully initialized and ready.

```javascript
window.OpenSesameCellConfig.onReady = function() {
  console.log('Widget is ready!');
  // Widget is now fully initialized
};
```

**Note:** You can also check `window.OpenSesameCellConfig.ready` (boolean) to check if widget is ready.

### `onShow`

Called when the widget is shown/expanded.

```javascript
window.OpenSesameCellConfig.onShow = function() {
  console.log('Widget was shown');
  // Track analytics, update UI, etc.
  window.OpenSesameCellConfig.trackEvent('widget-opened');
};
```

### `onHide`

Called when the widget is hidden/collapsed.

```javascript
window.OpenSesameCellConfig.onHide = function() {
  console.log('Widget was hidden');
  // Track analytics, update UI, etc.
  window.OpenSesameCellConfig.trackEvent('widget-closed');
};
```

### `onMessage`

Called when a new assistant message is received.

```javascript
window.OpenSesameCellConfig.onMessage = function(message) {
  console.log('New message received:', message);
  // {
  //   messageId: 'msg-123',
  //   type: 'assistant',
  //   content: 'Hello! How can I help you?',
  //   timestamp: '2024-01-01T12:00:00.000Z',
  //   threadId: 'thread-456'
  // }
  
  // Trigger notifications, analytics, etc.
  showNotification('New message from assistant');
  window.OpenSesameCellConfig.trackEvent('message-received', {
    messageId: message.messageId,
    threadId: message.threadId
  });
};
```

**Message object:**
- `messageId` (string): Unique message identifier
- `type` (string): Message type (`'assistant'`)
- `content` (string): Message content
- `timestamp` (string): Message timestamp (ISO format)
- `threadId` (string): Thread/conversation ID

### `onError`

Called when an error occurs in the widget.

```javascript
window.OpenSesameCellConfig.onError = function(error) {
  console.error('Widget error:', error);
  // {
  //   type: 'initialization',
  //   message: 'Failed to fetch configuration',
  //   error: Error(...),
  //   context: { cellId: 'your-cell-id' },
  //   timestamp: '2024-01-01T12:00:00.000Z'
  // }
  
  // Handle errors: show fallback UI, retry, send to error tracking service, etc.
  if (error.type === 'initialization') {
    // Widget failed to initialize - retry after a delay
    setTimeout(() => {
      window.OpenSesameCellConfig.retry().catch(err => {
        console.error('Retry failed:', err);
      });
    }, 2000);
  } else if (error.type === 'network') {
    // Network error - show offline message
    showOfflineMessage();
  }
  
  // Send to error tracking service
  sendToErrorTracking(error);
};
```

**Error types:**
- `'initialization'`: Widget failed to initialize
- `'rendering'`: Widget failed to render
- `'react_boundary'`: React component error
- `'network'`: Network/API error

### `onUnreadCountChange`

Called when the unread message count changes.

```javascript
window.OpenSesameCellConfig.onUnreadCountChange = function(count) {
  console.log('Unread count:', count);
  // Update badge, notification, etc.
  updateUnreadBadge(count);
};
```

**Note:** This callback is available but unread count tracking is not yet fully implemented.

---

## Error Handling

The widget includes comprehensive error handling to ensure it doesn't break your application.

### Automatic Error Handling

- Widget errors are caught and logged to console
- Widget continues to function even if some features fail
- Errors are stored in global error state for inspection

### Manual Error Handling

```javascript
// Check for errors
if (window.OpenSesameCellConfig.hasError) {
  const error = window.OpenSesameCellConfig.getError();
  console.error('Widget error:', error);
  
  // Handle based on error type
  switch (error.type) {
    case 'initialization':
      // Retry initialization
      await window.OpenSesameCellConfig.retry();
      break;
    case 'network':
      // Show offline message
      showOfflineMessage();
      break;
    default:
      // Handle other errors
      break;
  }
}
```

### Error Recovery

```javascript
// Set up error callback
window.OpenSesameCellConfig.onError = function(error) {
  if (error.type === 'initialization') {
    // Auto-retry after delay
    setTimeout(async () => {
      try {
        await window.OpenSesameCellConfig.retry();
        console.log('Widget recovered successfully');
      } catch (retryError) {
        console.error('Recovery failed:', retryError);
        // Show fallback UI
        showFallbackWidget();
      }
    }, 2000);
  }
};
```

---

## Examples

### Basic Integration

```html
<!DOCTYPE html>
<html>
<head>
  <title>My App</title>
</head>
<body>
  <h1>Welcome to My App</h1>
  
  <script>
    window.OpenSesameCellConfig = {
      cellId: 'your-cell-id-here'
    };
  </script>
  <script src="https://cell.opensesame.dev/widget.js" async></script>
</body>
</html>
```

### With User Identification

```javascript
// Identify user when they log in
function onUserLogin(user) {
  window.OpenSesameCellConfig.identify({
    user_id: user.id,
    email: user.email,
    name: user.name,
    company: {
      id: user.companyId,
      name: user.companyName
    },
    custom_attributes: {
      plan: user.plan,
      signup_date: user.signupDate
    }
  });
}

// Update user data when it changes
function onUserUpdate(user) {
  window.OpenSesameCellConfig.update({
    email: user.newEmail,
    custom_attributes: {
      plan: user.newPlan
    }
  });
}
```

### With Event Tracking

```javascript
// Track page views
window.addEventListener('load', () => {
  window.OpenSesameCellConfig.trackPageView({
    title: document.title,
    path: window.location.pathname
  });
});

// Track custom events
function onPurchaseComplete(order) {
  window.OpenSesameCellConfig.trackEvent('purchase-completed', {
    price: order.total,
    currency: order.currency,
    product: order.productName,
    order_id: order.id
  });
}

function onButtonClick(buttonName) {
  window.OpenSesameCellConfig.trackEvent('button-clicked', {
    button: buttonName,
    page: window.location.pathname
  });
}
```

### With Dynamic Theming

```javascript
// Update widget colors based on user preference
function applyTheme(theme) {
  window.OpenSesameCellConfig.updateConfig({
    primaryColor: theme.primaryColor,
    secondaryColor: theme.secondaryColor,
    textColor: theme.textColor
  });
}

// Update widget size based on screen size
function updateWidgetSize() {
  const isMobile = window.innerWidth < 768;
  window.OpenSesameCellConfig.updateConfig({
    defaultWidth: isMobile ? 400 : 800,
    bottomMargin: isMobile ? 15 : 25
  });
}

window.addEventListener('resize', updateWidgetSize);
```

### With Error Handling

```javascript
// Set up comprehensive error handling
window.OpenSesameCellConfig.onError = function(error) {
  // Log to console
  console.error('Widget error:', error);
  
  // Send to error tracking service
  if (window.errorTrackingService) {
    window.errorTrackingService.captureException(error.error, {
      tags: {
        widget: true,
        errorType: error.type
      },
      extra: error.context
    });
  }
  
  // Handle based on error type
  switch (error.type) {
    case 'initialization':
      // Show retry button
      showRetryButton(() => {
        window.OpenSesameCellConfig.retry();
      });
      break;
      
    case 'network':
      // Show offline indicator
      showOfflineIndicator();
      break;
      
    default:
      // Show generic error message
      showErrorMessage('Widget temporarily unavailable');
  }
};

// Check widget state on page load
window.addEventListener('load', () => {
  const state = window.OpenSesameCellConfig.getState();
  if (state.hasError) {
    console.warn('Widget has an error:', state.error);
  }
});
```

### With Callbacks

```javascript
// Track widget interactions
window.OpenSesameCellConfig.onShow = function() {
  console.log('Widget opened');
  window.OpenSesameCellConfig.trackEvent('widget-opened');
  
  // Pause video when widget opens
  pauseBackgroundVideo();
};

window.OpenSesameCellConfig.onHide = function() {
  console.log('Widget closed');
  window.OpenSesameCellConfig.trackEvent('widget-closed');
  
  // Resume video when widget closes
  resumeBackgroundVideo();
};

window.OpenSesameCellConfig.onMessage = function(message) {
  console.log('New message:', message.content);
  
  // Show browser notification
  if (Notification.permission === 'granted') {
    new Notification('New message', {
      body: message.content,
      icon: '/icon.png'
    });
  }
  
  // Update page title with unread count
  updatePageTitle();
};

window.OpenSesameCellConfig.onReady = function() {
  console.log('Widget ready');
  
  // Identify user once widget is ready
  if (window.currentUser) {
    window.OpenSesameCellConfig.identify({
      user_id: window.currentUser.id,
      email: window.currentUser.email
    });
  }
};
```

### Complete Integration Example

```html
<!DOCTYPE html>
<html>
<head>
  <title>My App with OpenSesame Widget</title>
</head>
<body>
  <h1>Welcome to My App</h1>
  
  <script>
    // Initialize widget configuration
    window.OpenSesameCellConfig = {
      cellId: 'your-cell-id-here',
      primaryColor: '#5E17EB',
      borderRadius: 32
    };
    
    // Set up callbacks
    window.OpenSesameCellConfig.onReady = function() {
      console.log('Widget is ready!');
      
      // Identify user when widget is ready
      if (window.currentUser) {
        window.OpenSesameCellConfig.identify({
          user_id: window.currentUser.id,
          email: window.currentUser.email,
          name: window.currentUser.name
        });
      }
      
      // Track page view
      window.OpenSesameCellConfig.trackPageView();
    };
    
    window.OpenSesameCellConfig.onShow = function() {
      window.OpenSesameCellConfig.trackEvent('widget-opened');
    };
    
    window.OpenSesameCellConfig.onHide = function() {
      window.OpenSesameCellConfig.trackEvent('widget-closed');
    };
    
    window.OpenSesameCellConfig.onMessage = function(message) {
      console.log('New message:', message);
      // Handle new messages (notifications, etc.)
    };
    
    window.OpenSesameCellConfig.onError = function(error) {
      console.error('Widget error:', error);
      if (error.type === 'initialization') {
        setTimeout(() => {
          window.OpenSesameCellConfig.retry();
        }, 2000);
      }
    };
  </script>
  
  <script src="https://cell.opensesame.dev/widget.js" async></script>
</body>
</html>
```

---

## Troubleshooting

### Widget Not Appearing

1. **Check browser console** for errors
2. **Verify cellId** is correct
3. **Check network tab** to ensure widget.js loads successfully
4. **Verify ready state:**
   ```javascript
   console.log('Widget ready:', window.OpenSesameCellConfig.ready);
   ```

### Widget Not Responding

1. **Check widget state:**
   ```javascript
   const state = window.OpenSesameCellConfig.getState();
   console.log('Widget state:', state);
   ```

2. **Check for errors:**
   ```javascript
   if (window.OpenSesameCellConfig.hasError) {
     const error = window.OpenSesameCellConfig.getError();
     console.error('Error:', error);
   }
   ```

3. **Try retry:**
   ```javascript
   await window.OpenSesameCellConfig.retry();
   ```

### Authentication Issues

1. **Verify auth headers** are set correctly:
   ```javascript
   window.OpenSesameCellConfig.config.auth = {
     'Authorization': 'Bearer your-token-here'
   };
   ```

2. **Check token expiration** and refresh if needed

### Styling Issues

1. **Verify CSS is loaded** (widget injects it automatically, but you can add it manually)
2. **Check for CSS conflicts** with your site's styles
3. **Use updateConfig** to adjust styling:
   ```javascript
   window.OpenSesameCellConfig.updateConfig({
     primaryColor: '#FF0000',
     borderRadius: 20
   });
   ```

### API Methods Not Working

1. **Wait for widget to be ready:**
   ```javascript
   function waitForWidget() {
     return new Promise((resolve) => {
       if (window.OpenSesameCellConfig.ready) {
         resolve();
       } else {
         window.OpenSesameCellConfig.onReady = resolve;
       }
     });
   }
   
   await waitForWidget();
   // Now use API methods
   ```

2. **Check method exists:**
   ```javascript
   if (typeof window.OpenSesameCellConfig.show === 'function') {
     window.OpenSesameCellConfig.show();
   }
   ```

### Common Issues

**Issue:** Widget shows but doesn't respond to clicks
- **Solution:** Check for z-index conflicts or overlay elements

**Issue:** Widget appears in wrong position
- **Solution:** Adjust `bottomMargin` in config or use `updateConfig()`

**Issue:** Events not being tracked
- **Solution:** Verify user is identified and check network tab for API calls

**Issue:** Callbacks not firing
- **Solution:** Ensure callbacks are set before widget initializes, or set them in `onReady`

---

## Best Practices

1. **Always wait for widget to be ready** before using API methods
2. **Identify users early** for better analytics and personalization
3. **Track important events** to understand user behavior
4. **Handle errors gracefully** with fallback UI
5. **Use updateConfig** for dynamic theming based on user preferences
6. **Set up callbacks** to react to widget lifecycle events
7. **Monitor widget state** in production for debugging

---

## Support

For additional help:
- Check the [OpenSesame Documentation](https://docs.opensesame.dev)
- Contact support at support@opensesame.dev
- Report issues on GitHub

---

## Changelog

### Version 1.0.0
- Initial release
- Full widget API with show/hide/destroy
- User identification and event tracking
- Runtime configuration updates
- Comprehensive error handling
- Event callbacks (onShow, onHide, onMessage, onError)
- Widget state API

---

*Last updated: January 2024*

