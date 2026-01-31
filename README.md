-- LocalScript (botão "Use")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local adminSpamEvent = ReplicatedStorage:WaitForChild("AdminSpamEvent")

local button = script.Parent -- coloque este LocalScript dentro do TextButton
local cooldown = 120
local lastUse = 0

local function setButtonText(text)
    if button:IsA("TextButton") then
        button.Text = text
    end
end

button.MouseButton1Click:Connect(function()
    local now = tick()
    if now - lastUse < cooldown then
        local remain = math.ceil(cooldown - (now - lastUse))
        setButtonText("Cooldown: "..remain.."s")
        return
    end

    local me = Players.LocalPlayer
    local allPlayers = Players:GetPlayers()

    if #allPlayers <= 1 then
        -- Só você no servidor: não pode usar
        setButtonText("Nenhum alvo")
        wait(2)
        setButtonText("Use")
        return
    end

    -- escolhe um alvo diferente de você (aqui pega o primeiro que não for você)
    local target
    for _, p in ipairs(allPlayers) do
        if p ~= me then
            target = p
            break
        end
    end
    if not target then
        setButtonText("Sem alvo")
        wait(2)
        setButtonText("Use")
        return
    end

    -- pede ao servidor para executar (servidor fará validações e cooldown)
    adminSpamEvent:FireServer(target.UserId)

    lastUse = now
    setButtonText("Usado - 120s")
    -- opcional: voltar o texto depois
    delay(2, function() setButtonText("Use") end)
end)
