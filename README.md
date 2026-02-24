local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local sg = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
sg.Name = "AutoFarm_V2"

local Brainrots = {
    "Tim Cheese", "LiriliLarila", "Fluri Flura", "Cacto Hipopotamo",
    "Pipi Potato", "Tric Trac Barabum", "Burbaloni Loliloli", "Boneca Ambalabu",
    "Trippi Troppi", "Svinina Bombardino", "Bambini Crostini", "Avocadini Guffo",
    "Bandito Bobrito", "Tatatata Sahur",
    "Gangster Footera", "Tung Tung Sahur", "Cappuccino Assassino", "Pipi Kiwi",
    "Orangutini Ananassini", "Talpa Di Fero", "Espresso Signora",
    "Brr Brr Patapim", "Rhino Toasterino", "Brr Bicus Dicus",
    "Strawberrelli Flamingelli", "Bananito Delfinito", "Balerina Capucina", "Glorbo Fruttodrillo",
    "Blueberrinni Octopusini", "Chimpanzini Bananini", "Bombardiro Crocodilo", "Elefanto Cocofanto",
    "Bombombini Gusini", "Pandaccini Bananini", "Chef Crabracadabra",
    "Gorillo Watermelondrillo", "Frigo Camelo", "Girafa Celestre",
    "Ganganzelli Trulala", "Tigroligre Frutonni",
    "Tralalerodon", "Esok", "La Vaca", "Strawberry",
    "Capitano Clash Warnini", "Meowl", "Garama", "Madundung"
}

local Mutacoes = { "Ouro", "Diamante", "Rainbow", "Galaxy" }

local MutTraducao = {
    ["ouro"] = {"ouro", "gold"},
    ["diamante"] = {"diamante", "diamond"},
    ["rainbow"] = {"rainbow"},
    ["galaxy"] = {"galaxy"},
}

local MutCores = {
    ["Ouro"] = Color3.fromRGB(255, 200, 0),
    ["Diamante"] = Color3.fromRGB(100, 200, 255),
    ["Rainbow"] = Color3.fromRGB(255, 100, 200),
    ["Galaxy"] = Color3.fromRGB(180, 100, 255),
}

local AbaCores = {
    Normal = Color3.fromRGB(200,200,200),
    Gold = Color3.fromRGB(255,200,0),
    Diamond = Color3.fromRGB(100,200,255),
    Rainbow = Color3.fromRGB(255,100,200),
    Galaxy = Color3.fromRGB(180,100,255),
}

local AlvosSelecionados = {}
local MutacoesSelecionadas = {}
local FarmAtivo = false
local AutoIndexAtivo = false
local AutoIndexAba = "Normal"

-- brainrots já coletados nessa sessão (auto index aprende com tentativas)
local ColetadosIndex = {}

local dragging, dragInput, dragStart, startPos = false, nil, nil, nil

-- // UI PRINCIPAL
local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(400, 530)
Main.Position = UDim2.new(0.5, -200, 0.25, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)
local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(0, 255, 127)
stroke.Thickness = 2

local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundTransparency = 1

local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -50, 1, 0)
Title.Text = "AUTO FARM INFINITO"
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

local CloseBtn = Instance.new("TextButton", TopBar)
CloseBtn.Size = UDim2.fromOffset(30, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 7)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.new(1,1,1)
CloseBtn.BackgroundColor3 = Color3.fromRGB(40,40,40)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 16
CloseBtn.BorderSizePixel = 0
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0,6)
CloseBtn.MouseEnter:Connect(function() CloseBtn.BackgroundColor3 = Color3.fromRGB(180,30,30) end)
CloseBtn.MouseLeave:Connect(function() CloseBtn.BackgroundColor3 = Color3.fromRGB(40,40,40) end)
CloseBtn.MouseButton1Click:Connect(function() FarmAtivo=false AutoIndexAtivo=false sg:Destroy() end)

local function MakeLabel(parent, pos, text, color)
    local l = Instance.new("TextLabel", parent)
    l.Size = UDim2.new(0.92, 0, 0, 20)
    l.Position = pos
    l.Text = text
    l.TextColor3 = color or Color3.fromRGB(0,255,127)
    l.BackgroundTransparency = 1
    l.Font = Enum.Font.GothamBold
    l.TextSize = 13
    l.TextXAlignment = Enum.TextXAlignment.Left
    return l
end

local function MakeToggle(parent, pos, text)
    local b = Instance.new("TextButton", parent)
    b.Size = UDim2.new(0.92, 0, 0, 42)
    b.Position = pos
    b.Text = text
    b.BackgroundColor3 = Color3.fromRGB(35,35,35)
    b.TextColor3 = Color3.new(1,1,1)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 17
    b.BorderSizePixel = 0
    Instance.new("UICorner", b)
    return b
end

-- AUTO FARM toggle
local Toggle = MakeToggle(Main, UDim2.new(0.04,0,0,50), "AUTO FARM: OFF")
Toggle.MouseButton1Click:Connect(function()
    FarmAtivo = not FarmAtivo
    if FarmAtivo then
        AutoIndexAtivo = false
    end
    Toggle.Text = FarmAtivo and "FARMANDO... (ON)" or "AUTO FARM: OFF"
    Toggle.BackgroundColor3 = FarmAtivo and Color3.fromRGB(0,100,50) or Color3.fromRGB(35,35,35)
end)

-- BRAINROTS lista
MakeLabel(Main, UDim2.new(0.04,0,0,100), "BRAINROTS:")
local ListBrainrot = Instance.new("ScrollingFrame", Main)
ListBrainrot.Size = UDim2.new(0.92,0,0,110)
ListBrainrot.Position = UDim2.new(0.04,0,0,122)
ListBrainrot.CanvasSize = UDim2.new(0,0,0,#Brainrots*32)
ListBrainrot.BackgroundColor3 = Color3.fromRGB(25,25,25)
ListBrainrot.ScrollBarThickness = 4
ListBrainrot.BorderSizePixel = 0
Instance.new("UICorner", ListBrainrot)
Instance.new("UIListLayout", ListBrainrot)

for _, name in ipairs(Brainrots) do
    local b = Instance.new("TextButton", ListBrainrot)
    b.Size = UDim2.new(1,0,0,32)
    b.Text = name
    b.BackgroundColor3 = Color3.fromRGB(30,30,30)
    b.TextColor3 = Color3.new(0.7,0.7,0.7)
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.BorderSizePixel = 0
    b.MouseButton1Click:Connect(function()
        local lower = name:lower()
        if AlvosSelecionados[lower] then
            AlvosSelecionados[lower] = nil
            b.TextColor3 = Color3.new(0.7,0.7,0.7)
            b.BackgroundColor3 = Color3.fromRGB(30,30,30)
        else
            AlvosSelecionados[lower] = true
            b.TextColor3 = Color3.fromRGB(0,255,127)
            b.BackgroundColor3 = Color3.fromRGB(20,50,35)
        end
        local count = 0
        for _ in pairs(AlvosSelecionados) do count+=1 end
        Title.Text = count==0 and "AUTO FARM INFINITO" or count.." BRAINROT(S)"
    end)
end

-- MUTAÇÕES lista
MakeLabel(Main, UDim2.new(0.04,0,0,242), "MUTAÇÕES (opcional):")
local ListMut = Instance.new("ScrollingFrame", Main)
ListMut.Size = UDim2.new(0.92,0,0,90)
ListMut.Position = UDim2.new(0.04,0,0,264)
ListMut.CanvasSize = UDim2.new(0,0,0,#Mutacoes*32)
ListMut.BackgroundColor3 = Color3.fromRGB(25,25,25)
ListMut.ScrollBarThickness = 4
ListMut.BorderSizePixel = 0
Instance.new("UICorner", ListMut)
Instance.new("UIListLayout", ListMut)

for _, name in ipairs(Mutacoes) do
    local b = Instance.new("TextButton", ListMut)
    b.Size = UDim2.new(1,0,0,32)
    b.Text = name
    b.BackgroundColor3 = Color3.fromRGB(30,30,30)
    b.TextColor3 = Color3.new(0.7,0.7,0.7)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 14
    b.BorderSizePixel = 0
    b.MouseButton1Click:Connect(function()
        local lower = name:lower()
        if MutacoesSelecionadas[lower] then
            MutacoesSelecionadas[lower] = nil
            b.TextColor3 = Color3.new(0.7,0.7,0.7)
            b.BackgroundColor3 = Color3.fromRGB(30,30,30)
        else
            MutacoesSelecionadas[lower] = true
            b.TextColor3 = MutCores[name]
            b.BackgroundColor3 = Color3.fromRGB(30,30,30)
        end
    end)
end

-- AUTO INDEX seção
MakeLabel(Main, UDim2.new(0.04,0,0,364), "AUTO INDEX:", Color3.fromRGB(0,200,255))

local AbaFrame = Instance.new("Frame", Main)
AbaFrame.Size = UDim2.new(0.92,0,0,32)
AbaFrame.Position = UDim2.new(0.04,0,0,386)
AbaFrame.BackgroundTransparency = 1
local AbaLayout = Instance.new("UIListLayout", AbaFrame)
AbaLayout.FillDirection = Enum.FillDirection.Horizontal
AbaLayout.Padding = UDim.new(0,4)

local Abas = {"Normal","Gold","Diamond","Rainbow","Galaxy"}
local AbaBtns = {}
for _, aba in ipairs(Abas) do
    local btn = Instance.new("TextButton", AbaFrame)
    btn.Size = UDim2.new(0,68,1,0)
    btn.Text = aba
    btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
    btn.TextColor3 = Color3.new(0.6,0.6,0.6)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.BorderSizePixel = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,6)
    AbaBtns[aba] = btn
    btn.MouseButton1Click:Connect(function()
        AutoIndexAba = aba
        ColetadosIndex = {} -- reseta ao trocar aba
        for _, b in pairs(AbaBtns) do
            b.TextColor3 = Color3.new(0.6,0.6,0.6)
            b.BackgroundColor3 = Color3.fromRGB(30,30,30)
        end
        btn.TextColor3 = AbaCores[aba]
        btn.BackgroundColor3 = Color3.fromRGB(20,35,50)
    end)
end
AbaBtns["Normal"].TextColor3 = AbaCores["Normal"]
AbaBtns["Normal"].BackgroundColor3 = Color3.fromRGB(20,35,50)

local ToggleIndex = MakeToggle(Main, UDim2.new(0.04,0,0,426), "AUTO INDEX: OFF")
ToggleIndex.MouseButton1Click:Connect(function()
    AutoIndexAtivo = not AutoIndexAtivo
    if AutoIndexAtivo then
        FarmAtivo = false
        Toggle.Text = "AUTO FARM: OFF"
        Toggle.BackgroundColor3 = Color3.fromRGB(35,35,35)
    end
    ToggleIndex.Text = AutoIndexAtivo and "AUTO INDEX: ON" or "AUTO INDEX: OFF"
    ToggleIndex.BackgroundColor3 = AutoIndexAtivo and Color3.fromRGB(0,80,120) or Color3.fromRGB(35,35,35)
end)

local StatusIndex = Instance.new("TextLabel", Main)
StatusIndex.Size = UDim2.new(0.92,0,0,20)
StatusIndex.Position = UDim2.new(0.04,0,0,475)
StatusIndex.Text = ""
StatusIndex.TextColor3 = Color3.fromRGB(0,200,255)
StatusIndex.BackgroundTransparency = 1
StatusIndex.Font = Enum.Font.Gotham
StatusIndex.TextSize = 12
StatusIndex.TextXAlignment = Enum.TextXAlignment.Left

-- ARRASTAR
TopBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging=true dragStart=i.Position startPos=Main.Position
    end
end)
TopBar.InputChanged:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseMovement then dragInput=i end
end)
UserInputService.InputChanged:Connect(function(i)
    if i==dragInput and dragging then
        local d = i.Position-dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset+d.X, startPos.Y.Scale, startPos.Y.Offset+d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then dragging=false end
end)

-- // FUNÇÕES CORE

local function GetTextoCompleto(prompt)
    local t = {}
    local p = prompt.Parent
    if p then
        table.insert(t, p.Name:lower())
        if p.Parent then table.insert(t, p.Parent.Name:lower()) end
        for _, desc in ipairs(p:GetDescendants()) do
            if desc:IsA("TextLabel") or desc:IsA("TextBox") then
                pcall(function() table.insert(t, desc.Text:lower()) end)
            end
            pcall(function()
                local v = desc:GetAttribute("Mutation")
                if type(v)=="string" then table.insert(t, v:lower()) end
            end)
        end
        pcall(function()
            local v = p:GetAttribute("Mutation")
            if type(v)=="string" then table.insert(t, v:lower()) end
        end)
    end
    table.insert(t, (prompt.ObjectText or ""):lower())
    table.insert(t, (prompt.ActionText or ""):lower())
    return table.concat(t, " ")
end

local function ContemPalavra(texto, palavra)
    return texto:find("%f[%a]"..palavra:lower().."%f[%A]") ~= nil
end

local function FindPrompt(alvo, mutacaoKey)
    -- mutacaoKey: nil = qualquer, "normal" = sem mutação, "gold" etc = com mutação
    for _, obj in pairs(workspace:GetDescendants()) do
        if not obj:IsA("ProximityPrompt") then continue end
        local texto = GetTextoCompleto(obj)
        if not ContemPalavra(texto, alvo) then continue end

        if mutacaoKey == nil or mutacaoKey == "" then
            -- auto farm manual: respeita MutacoesSelecionadas
            if next(MutacoesSelecionadas) == nil then return obj end
            for mut in pairs(MutacoesSelecionadas) do
                local vars = MutTraducao[mut] or {mut}
                for _, v in ipairs(vars) do
                    if ContemPalavra(texto, v) then return obj end
                end
            end
        elseif mutacaoKey == "normal" then
            -- index normal: pega qualquer prompt do brainrot
            return obj
        else
            -- index com mutação específica
            local vars = MutTraducao[mutacaoKey] or {mutacaoKey}
            for _, v in ipairs(vars) do
                if ContemPalavra(texto, v) then return obj end
            end
        end
    end
    return nil
end

local function FarmPrompt(prompt)
    local item = prompt.Parent
    local part = item:IsA("BasePart") and item or item:FindFirstChildWhichIsA("BasePart")
    if not part then return false end
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    local ok = pcall(function()
        hrp.CFrame = part.CFrame * CFrame.new(0,2,0)
        task.wait(0.05)
        prompt.HoldDuration = 0
        fireproximityprompt(prompt)
        task.wait(0.3)
    end)
    return ok
end

-- // LOOP AUTO FARM MANUAL
task.spawn(function()
    while true do
        task.wait(0.1)
        if not FarmAtivo or next(AlvosSelecionados)==nil then continue end
        for alvo in pairs(AlvosSelecionados) do
            local p = FindPrompt(alvo, nil)
            if p then FarmPrompt(p) end
        end
    end
end)

-- // LOOP AUTO INDEX
-- Lógica: percorre todos os brainrots da lista, tenta achar no workspace com a mutação certa.
-- Se achou e farmou com sucesso, marca como coletado nessa sessão e passa pro próximo.
-- Se não achou no workspace, pula (brainrot pode não estar spawned).
task.spawn(function()
    while true do
        task.wait(0.3)
        if not AutoIndexAtivo then continue end

        local mutKey = AutoIndexAba:lower() == "normal" and "normal" or AutoIndexAba:lower()
        -- gold/diamond/rainbow/galaxy -> usa MutTraducao
        local pendentes = {}
        for _, nome in ipairs(Brainrots) do
            if not ColetadosIndex[nome:lower()] then
                table.insert(pendentes, nome)
            end
        end

        if #pendentes == 0 then
            StatusIndex.Text = "Index completa! Resetando..."
            task.wait(3)
            ColetadosIndex = {}
            continue
        end

        local alvo = pendentes[1]
        StatusIndex.Text = "Faltam "..#pendentes.." | Farmando: "..alvo

        local prompt = FindPrompt(alvo:lower(), mutKey)
        if prompt then
            local ok = FarmPrompt(prompt)
            if ok then
                -- marca como coletado e passa pro próximo
                ColetadosIndex[alvo:lower()] = true
            end
        else
            -- não achou no workspace agora, tenta o próximo da lista
            -- (o atual será tentado de novo depois)
            StatusIndex.Text = "Procurando: "..alvo.." (não spawned, tentando próximo)"
            -- tenta o segundo pendente se houver
            if #pendentes > 1 then
                local alvo2 = pendentes[2]
                local p2 = FindPrompt(alvo2:lower(), mutKey)
                if p2 then
                    local ok2 = FarmPrompt(p2)
                    if ok2 then
                        ColetadosIndex[alvo2:lower()] = true
                        StatusIndex.Text = "Coletado: "..alvo2
                    end
                end
            end
            task.wait(0.5)
        end
    end
end)
