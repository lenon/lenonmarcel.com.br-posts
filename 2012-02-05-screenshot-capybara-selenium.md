---
layout: post
title:  Salvando screenshots da tela quando algum teste falha no Capybara/Selenium
date:   2012-02-05 22:44:32
---
Quando você está executando um teste de integração e este teste falha, ver a
tela no momento da falha ajuda bastante. Pra isso, você pode usar soluções como
o [capybara-screenshot](https://rubygems.org/gems/capybara-screenshot) ou algo
mais simples, sem nenhuma dependência extra:

    RSpec.configure do |config|
      config.after(:each) do
        if example.exception
          file = Rails.root.join "tmp/capybara-#{Time.now.to_i}.png"
          Capybara.page.driver.browser.save_screenshot file
        end
      end
    end

Note que isto funciona apenas quando o Selenium está configurado como driver.
