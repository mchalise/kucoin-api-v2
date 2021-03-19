# Kucoin API Ruby Snippet for Server Connection
Ruby Snippet Code to Connect To Kucoin API v2

  ```ruby 
  BASE_URL = 'https://api.kucoin.com/api/v1'.freeze
  
  def self.call_kucoin(url, type, data)
   full_url = "#{BASE_URL}#{url}"
    now = (Time.now.to_f * 1000).to_i.to_s
    str_to_sign = now+"#{type}/api/v1#{url}"
    signature = ::Base64.strict_encode64(
      ::OpenSSL::HMAC.digest(
        ::OpenSSL::Digest.new('sha256'),
        data[:api_secret].encode("utf-8"), str_to_sign.encode("utf-8")
        )
      )
    passphrase = ::Base64.strict_encode64(
      ::OpenSSL::HMAC.digest(
        ::OpenSSL::Digest.new('sha256'),
        data[:api_secret].encode("utf-8"), data[:api_passphrase].encode("utf-8")
        )
      )
    headers = {
      "Content-Type": "application/json",
      "KC-API-SIGN": signature,
      "KC-API-TIMESTAMP": now,
      "KC-API-KEY": data[:api_key],
      "KC-API-PASSPHRASE": passphrase,
      "KC-API-KEY-VERSION": '2'
    }  
    resp = HTTParty.get(full_url, headers: headers, format: :json)
    JSON.parse(resp.body)
  end 
  
  accounts = call_kucoin("/accounts", 'GET', data)
  
