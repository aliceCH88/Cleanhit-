// CleanHit - Single-file React + Stripe starter (Vite / Create React App compatible)
// -------------------------------------------------------------
// Files included (single-file bundle for preview):
// - src/App.jsx       -> main React app
// - src/main.jsx      -> render
// - src/styles.css    -> basic Tailwind-like utility classes (assumes Tailwind available in project)
// - server/index.js   -> Express server for Stripe Checkout (deploy as serverless function or small Node server)
//
// Quick setup instructions (summary at top of file):
// 1) Create a React app (Vite recommended): `npm create vite@latest cleanhit --template react`
// 2) Install dependencies: `npm install stripe axios` and add Tailwind (optional)
// 3) Copy server/index.js to a serverless function or small Node server. Set env vars: STRIPE_SECRET_KEY, DOMAIN (e.g., https://yourdomain.com), PRODUCT_PRICE_CENTS
// 4) Run frontend: `npm run dev`. Run server: `node server/index.js` (or deploy to Vercel/Render). Configure CORS if needed.
// 5) Use Stripe Dashboard to create product/prices or let server create a Checkout Session with a price in cents.
//
// NOTE: This is a starter template. Replace text, images, and branding as needed.

// ------------------- src/App.jsx -------------------
import React from 'react'
import axios from 'axios'

const PRODUCT = {
  id: 'cleanhit-kit-001',
  name: 'CleanHit Heavy Metal Test Kit',
  price_cents: 2999,
  price_display: '$29.99',
  description: 'Rapid heavy-metal screening kit for disposable vapes. Lab-grade filter and mail-in analysis option.',
  img: 'https://images.unsplash.com/photo-1582719478250-42d7d4a8b4f0?auto=format&fit=crop&w=800&q=60'
}

export default function App(){
  const [loading, setLoading] = React.useState(false)
  const [error, setError] = React.useState(null)

  async function handleBuy(){
    setLoading(true); setError(null)
    try{
      const res = await axios.post('/api/create-checkout-session', {
        productId: PRODUCT.id,
        quantity: 1
      })
      const {url} = res.data
      // redirect to Stripe Checkout
      window.location.href = url
    }catch(err){
      console.error(err)
      setError('无法创建付款，请稍后再试。')
      setLoading(false)
    }
  }

  return (
    <div className="min-h-screen bg-gradient-to-b from-white to-gray-50 font-sans text-gray-900 p-6">
      <header className="max-w-4xl mx-auto py-6">
        <h1 className="text-3xl font-bold">CleanHit</h1>
        <p className="mt-1 text-gray-600">Vape Contamination — A hidden problem in life</p>
      </header>

      <main className="max-w-4xl mx-auto grid md:grid-cols-2 gap-8 items-start">
        <section>
          <img src={PRODUCT.img} alt="product" className="w-full rounded-2xl shadow-md" />
          <div className="mt-6 bg-white p-6 rounded-xl shadow-sm">
            <h2 className="text-2xl font-semibold">Do you really know what’s in your vape?</h2>
            <p className="mt-3 text-gray-700">Most people believe vaping is safer than smoking. But many vapes—especially cheap, disposable ones—contain more than just nicotine and flavoring.</p>
            <ul className="mt-3 space-y-2 text-sm text-gray-700">
              <li>• Tiny particles of chromium, nickel, lead, or tin can be released by heated metal parts.</li>
              <li>• These metals are often not listed on the label and have no smell or taste.</li>
              <li>• Popular brands have tested positive for unsafe metal levels in some lab reports.</li>
            </ul>
          </div>
        </section>

        <aside className="space-y-6">
          <div className="bg-white p-6 rounded-2xl shadow-sm">
            <h3 className="text-xl font-semibold">{PRODUCT.name}</h3>
            <p className="mt-2 text-gray-700">{PRODUCT.description}</p>
            <div className="mt-4 flex items-center justify-between">
              <div>
                <div className="text-2xl font-bold">{PRODUCT.price_display}</div>
                <div className="text-sm text-gray-500">Free standard shipping</div>
              </div>
              <button onClick={handleBuy} disabled={loading} className="px-4 py-2 bg-black text-white rounded-lg shadow hover:opacity-95 disabled:opacity-60">
                {loading ? '处理中...' : 'Buy now'}
              </button>
            </div>
            {error && <p className="mt-3 text-red-600">{error}</p>}
          </div>

          <div className="bg-white p-6 rounded-2xl shadow-sm">
            <h4 className="font-semibold">Types of heavy metals that may hurt you</h4>
            <table className="w-full mt-3 text-sm text-gray-700 table-auto">
              <tbody>
                <tr><td className="font-medium">Nickel (Ni)</td><td>From heating coils — Short: sore throat; Long: lung inflammation</td></tr>
                <tr><td className="font-medium">Chromium (Cr)</td><td>Stainless steel parts — Short: metallic taste; Long: carcinogen</td></tr>
                <tr><td className="font-medium">Lead (Pb)</td><td>Soldering — Short: headache; Long: brain damage</td></tr>
                <tr><td className="font-medium">Cadmium / Tin</td><td>Cheap metals — Short: nausea; Long: kidney/liver damage</td></tr>
              </tbody>
            </table>
          </div>

          <div className="bg-white p-6 rounded-2xl shadow-sm text-sm text-gray-700">
            <h4 className="font-semibold">What can this lead to?</h4>
            <ul className="mt-2 list-disc ml-5">
              <li>Lung disease, cancer, brain and nerve damage</li>
              <li>Hormonal imbalance and developmental issues in teens</li>
              <li>Organ failure in chronic exposure</li>
            </ul>
          </div>
        </aside>
      </main>

      <footer className="max-w-4xl mx-auto mt-12 text-center text-gray-500 text-sm">
        <p>© CleanHit — Detect heavy metals in disposable vapes. Not a medical device. For informational use only.</p>
      </footer>
    </div>
  )
}

// ------------------- src/main.jsx -------------------
import React from 'react'
import { createRoot } from 'react-dom/client'
import App from './App'
import './styles.css'

createRoot(document.getElementById('root')).render(<App />)

// ------------------- src/styles.css -------------------
/* Minimal styles (if not using Tailwind, you can insert your own CSS) */
body{font-family: Inter,ui-sans-serif,system-ui,-apple-system,'Segoe UI',Roboto,'Helvetica Neue',Arial}
img{max-width:100%}

/* Add Tailwind in project for full styling; these classes assume tailwind is present. */

// ------------------- server/index.js -------------------
// Small Express server to create Stripe Checkout sessions.
// Deploy as serverless function or keep as small Node server.

/*
Environment variables required:
- STRIPE_SECRET_KEY (your Stripe secret key)
- DOMAIN (your site domain, e.g., https://yourdomain.com)
Optionally:
- PRICE_CENTS or you can create a Stripe Price in dashboard and use price ID
*/

const express = require('express')
const app = express()
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY)
const cors = require('cors')

app.use(cors())
app.use(express.json())

app.post('/api/create-checkout-session', async (req, res) => {
  try{
    const { productId, quantity } = req.body
    const domain = process.env.DOMAIN || 'http://localhost:3000'

    // Create a Checkout Session with one-time payment
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      mode: 'payment',
      line_items: [{
        price_data: {
          currency: 'usd',
          product_data: { name: 'CleanHit Heavy Metal Test Kit' },
          unit_amount: 2999
        },
        quantity: quantity || 1
      }],
      success_url: `${domain}/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${domain}/cancel`
    })

    res.json({ url: session.url })
  }catch(err){
    console.error(err)
    res.status(500).json({error: '创建会话失败'})
  }
})

if(require.main === module){
  const port = process.env.PORT || 4242
  app.listen(port, ()=> console.log('Server listening on', port))
}

// ------------------- Deployment notes -------------------
/*
- Frontend: Deploy to Vercel / Netlify / Surge. Configure build (npm run build).
- Server: Deploy as a serverless function (Vercel Serverless Functions) or to Render/Heroku.
- Set env vars on host: STRIPE_SECRET_KEY, DOMAIN
- Use HTTPS domain for Stripe
- Testing: Use Stripe test keys and cards (4242 4242 4242 4242)
- Security: Do not expose STRIPE_SECRET_KEY in frontend. Use server endpoints only.
*/
