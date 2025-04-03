# 🔐 NextAuth 2FA Integration 

This project demonstrates how to integrate two-factor authentication (2FA) with NextAuth in a Next.js application using the Credentials provider. The provided solution allows users to authenticate with their email/password and optionally verify a 2FA token when required.

## 🚀 Overview

The code is a configuration for NextAuth using a credentials provider. It checks if the 2FA token is required by sending a login request to your API. If 2FA is required, it prompts for a 2FA token and verifies it before allowing login. This solution supports JWT-based sessions for user authentication.

## ✨ Features

- **Email/Password Authentication:** The user can log in with email and password.
- **Two-Factor Authentication (2FA):** If the server requires 2FA, the user will need to provide a 2FA token.
- **JWT Session Management:** After authentication, a JWT is created and managed for the user session.
- **Custom Sign-In Page:** The login page is customized, and 2FA is handled in a separate flow.

## ⚙️ Installation

### 📦 1. Install Dependencies

To use this code, you need to install the necessary dependencies in your Next.js project:

```bash
npm install next-auth axios
```
### 🛠️ 2. Create the API Route for Authentication

Create the file pages/api/auth/[...nextauth].ts in your project and paste the code from this repository.

### 📝 3. Create a Custom Sign-In Page

The authentication flow uses a custom login page located at /login. You need to create a page to handle user login and show the necessary forms for entering the 2FA token.

Create pages/login.tsx or pages/login.js:

```
import { signIn } from 'next-auth/react';
import { useState } from 'react';

export default function LoginPage() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [twoFAToken, setTwoFAToken] = useState('');
    const [userId, setUserId] = useState('');
    const [error, setError] = useState(null);

    const handleSubmit = async (e) => {
        e.preventDefault();

        const res = await signIn('credentials', {
            redirect: false,
            email,
            password,
            twoFAToken,
            userId
        });

        if (res?.error) {
            const parsedError = JSON.parse(res.error);
            if (parsedError.twofaRequired) {
                // Prompt the user to enter 2FA token
                setUserId(parsedError.userId);
                setError('Two-factor authentication required');
            } else {
                setError(res.error);
            }
        } else {
            // Successfully logged in
            window.location.href = '/dashboard'; // or wherever you want to redirect
        }
    };

    return (
        <div>
            <h2>Login</h2>
            <form onSubmit={handleSubmit}>
                <input
                    type="email"
                    placeholder="Email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                    required
                />
                <input
                    type="password"
                    placeholder="Password"
                    value={password}
                    onChange={(e) => setPassword(e.target.value)}
                    required
                />
                {error && <div>{error}</div>}
                {userId && (
                    <input
                        type="text"
                        placeholder="2FA Token"
                        value={twoFAToken}
                        onChange={(e) => setTwoFAToken(e.target.value)}
                        required
                    />
                )}
                <button type="submit">Log in</button>
            </form>
        </div>
    );
}
```

### ⚡4. Set up the API Routes

Your login and verify-login API endpoints should be configured to handle authentication requests, check if 2FA is required, and verify the 2FA token.

### 🌍 5. Configure Environment Variables

In your .env.local file, set the NEXTAUTH_SECRET and NEXTAUTH_URL environment variable:

```
NEXTAUTH_SECRET=your_secret_key
NEXTAUTH_URL=http://localhost:3000
```

Also, ensure that your authentication API endpoints (/api/auth/login and /api/auth/2fa/verify-login) are correctly set up on your backend server (for example, localhost:5000 in the example).

### 🧪 6. Test the Authentication Flow

When a user logs in with email and password, if 2FA is required, they will be prompted to enter a 2FA token.

After 2FA is verified, the user is granted access and a session is created with JWT-based authentication.


### 🎉 Conclusion

This setup provides a flexible integration of two-factor authentication in a Next.js application using NextAuth. By utilizing the credentials provider, the solution can be adapted for a variety of use cases and backend authentication flows, with support for 2FA integration.
