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

local AlvosSelecionados = {}
local MutacoesSelecionadas = {}
local FarmAtivo = false
local AutoIndexAtivo = false
local AutoIndexAba = "Normal"

local dragging = false
local dragInput = nil
local dragStart = nil
local startPos = nil

-- // FRAME PRINCIPAL
local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(400, 520)
Main.Position = UDim2.new(0.5, -200, 0.3, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(0, 255, 127)
stroke.Thickness = 2

-- // TOPBAR
local TopBar = Instance.new("Frame", Main)
TopBar.Size = UDim2.new(1, 0, 0, 45)
TopBar.BackgroundTransparency = 1

-- // TÍTULO
local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -50, 1, 0)
Title.Text = "AUTO FARM INFINITO"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

-- // BOTÃO FECHAR
local CloseBtn = Instance.new("TextButton", TopBar)
CloseBtn.Size = UDim2.fromOffset(30, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 7)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.new(1, 1, 1)
CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 16
CloseBtn.BorderSizePixel = 0
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseEnter:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(180, 30, 30)
end)
CloseBtn.MouseLeave:Connect(function()
    CloseBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end)
CloseBtn.MouseButton1Click:Connect(function()
    FarmAtivo = false
    AutoIndexAtivo = false
    sg:Destroy()
end)

-- // BOTÃO FARMAR
local Toggle = Instance.new("TextButton", Main)
Toggle.Size = UDim2.new(0.92, 0, 0, 42)
Toggle.Position = UDim2.new(0.04, 0, 0, 50)
Toggle.Text = "AUTO FARM: OFF"
Toggle.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Toggle.TextColor3 = Color3.new(1, 1, 1)
Toggle.Font = Enum.Font.GothamBold
Toggle.TextSize = 17
Toggle.BorderSizePixel = 0
Instance.new("UICorner", Toggle)

Toggle.MouseButton1Click:Connect(function()
    FarmAtivo = not FarmAtivo
    if FarmAtivo then AutoIndexAtivo = false end
    Toggle.Text = FarmAtivo and "FARMANDO... (ON)" or "AUTO FARM: OFF"
    Toggle.BackgroundColor3 = FarmAtivo and Color3.fromRGB(0, 100, 50) or Color3.fromRGB(35, 35, 35)
end)

-- // LABEL BRAINROTS
local LabelBrainrot = Instance.new("TextLabel", Main)
LabelBrainrot.Size = UDim2.new(0.92, 0, 0, 20)
LabelBrainrot.Position = UDim2.new(0.04, 0, 0, 100)
LabelBrainrot.Text = "BRAINROTS:"
LabelBrainrot.TextColor3 = Color3.fromRGB(0, 255, 127)
LabelBrainrot.BackgroundTransparency = 1
LabelBrainrot.Font = Enum.Font.GothamBold
LabelBrainrot.TextSize = 13
LabelBrainrot.TextXAlignment = Enum.TextXAlignment.Left

-- // LISTA BRAINROTS
local ListBrainrot = Instance.new("ScrollingFrame", Main)
ListBrainrot.Size = UDim2.new(0.92, 0, 0, 110)
ListBrainrot.Position = UDim2.new(0.04, 0, 0, 122)
ListBrainrot.CanvasSize = UDim2.new(0, 0, 0, #Brainrots * 32)
ListBrainrot.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ListBrainrot.ScrollBarThickness = 4
ListBrainrot.BorderSizePixel = 0
Instance.new("UICorner", ListBrainrot)
Instance.new("UIListLayout", ListBrainrot)

for _, name in ipairs(Brainrots) do
    local b = Instance.new("TextButton", ListBrainrot)
    b.Size = UDim2.new(1, 0, 0, 32)
    b.Text = name
    b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
    b.Font = Enum.Font.Gotham
    b.TextSize = 14
    b.BorderSizePixel = 0

    b.MouseButton1Click:Connect(function()
        local lower = name:lower()
        if AlvosSelecionados[lower] then
            AlvosSelecionados[lower] = nil
            b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        else
            AlvosSelecionados[lower] = true
            b.TextColor3 = Color3.fromRGB(0, 255, 127)
            b.BackgroundColor3 = Color3.fromRGB(20, 50, 35)
        end
        local count = 0
        for _ in pairs(AlvosSelecionados) do count = count + 1 end
        Title.Text = count == 0 and "AUTO FARM INFINITO" or count .. " BRAINROT(S)"
    end)
end

-- // LABEL MUTAÇÕES
local LabelMut = Instance.new("TextLabel", Main)
LabelMut.Size = UDim2.new(0.92, 0, 0, 20)
LabelMut.Position = UDim2.new(0.04, 0, 0, 242)
LabelMut.Text = "MUTAÇÕES (opcional):"
LabelMut.TextColor3 = Color3.fromRGB(0, 255, 127)
LabelMut.BackgroundTransparency = 1
LabelMut.Font = Enum.Font.GothamBold
LabelMut.TextSize = 13
LabelMut.TextXAlignment = Enum.TextXAlignment.Left

-- // LISTA MUTAÇÕES
local ListMut = Instance.new("ScrollingFrame", Main)
ListMut.Size = UDim2.new(0.92, 0, 0, 90)
ListMut.Position = UDim2.new(0.04, 0, 0, 264)
ListMut.CanvasSize = UDim2.new(0, 0, 0, #Mutacoes * 32)
ListMut.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ListMut.ScrollBarThickness = 4
ListMut.BorderSizePixel = 0
Instance.new("UICorner", ListMut)
Instance.new("UIListLayout", ListMut)

local MutCores = {
    ["Ouro"] = Color3.fromRGB(255, 200, 0),
    ["Diamante"] = Color3.fromRGB(100, 200, 255),
    ["Rainbow"] = Color3.fromRGB(255, 100, 200),
    ["Galaxy"] = Color3.fromRGB(180, 100, 255),
}

local MutTraducao = {
    ["ouro"] = {"ouro", "gold"},
    ["diamante"] = {"diamante", "diamond"},
    ["rainbow"] = {"rainbow"},
    ["galaxy"] = {"galaxy"},
}

for _, name in ipairs(Mutacoes) do
    local b = Instance.new("TextButton", ListMut)
    b.Size = UDim2.new(1, 0, 0, 32)
    b.Text = name
    b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 14
    b.BorderSizePixel = 0

    b.MouseButton1Click:Connect(function()
        local lower = name:lower()
        if MutacoesSelecionadas[lower] then
            MutacoesSelecionadas[lower] = nil
            b.TextColor3 = Color3.new(0.7, 0.7, 0.7)
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        else
            MutacoesSelecionadas[lower] = true
            b.TextColor3 = MutCores[name]
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        end
    end)
end

-- // LABEL AUTO INDEX
local LabelIndex = Instance.new("TextLabel", Main)
LabelIndex.Size = UDim2.new(0.92, 0, 0, 20)
LabelIndex.Position = UDim2.new(0.04, 0, 0, 364)
LabelIndex.Text = "AUTO INDEX:"
LabelIndex.TextColor3 = Color3.fromRGB(0, 200, 255)
LabelIndex.BackgroundTransparency = 1
LabelIndex.Font = Enum.Font.GothamBold
LabelIndex.TextSize = 13
LabelIndex.TextXAlignment = Enum.TextXAlignment.Left

-- // ABAS DO INDEX
local AbaFrame = Instance.new("Frame", Main)
AbaFrame.Size = UDim2.new(0.92, 0, 0, 32)
AbaFrame.Position = UDim2.new(0.04, 0, 0, 386)
AbaFrame.BackgroundTransparency = 1
local AbaLayout = Instance.new("UIListLayout", AbaFrame)
AbaLayout.FillDirection = Enum.FillDirection.Horizontal
AbaLayout.Padding = UDim.new(0, 4)

local Abas = {"Normal", "Gold", "Diamond", "Rainbow", "Galaxy"}
local AbaCores = {
    Normal = Color3.fromRGB(200,200,200),
    Gold = Color3.fromRGB(255,200,0),
    Diamond = Color3.fromRGB(100,200,255),
    Rainbow = Color3.fromRGB(255,100,200),
    Galaxy = Color3.fromRGB(180,100,255),
}
local AbaBtns = {}

for _, aba in ipairs(Abas) do
    local btn = Instance.new("TextButton", AbaFrame)
    btn.Size = UDim2.new(0, 68, 1, 0)
    btn.Text = aba
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.TextColor3 = Color3.new(0.6, 0.6, 0.6)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 11
    btn.BorderSizePixel = 0
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    AbaBtns[aba] = btn

    btn.MouseButton1Click:Connect(function()
        AutoIndexAba = aba
        for _, b in pairs(AbaBtns) do
            b.TextColor3 = Color3.new(0.6, 0.6, 0.6)
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        end
        btn.TextColor3 = AbaCores[aba]
        btn.BackgroundColor3 = Color3.fromRGB(20, 35, 50)
    end)
end
AbaBtns["Normal"].TextColor3 = AbaCores["Normal"]
AbaBtns["Normal"].BackgroundColor3 = Color3.fromRGB(20, 35, 50)

-- // BOTÃO AUTO INDEX
local ToggleIndex = Instance.new("TextButton", Main)
ToggleIndex.Size = UDim2.new(0.92, 0, 0, 42)
ToggleIndex.Position = UDim2.new(0.04, 0, 0, 426)
ToggleIndex.Text = "AUTO INDEX: OFF"
ToggleIndex.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
ToggleIndex.TextColor3 = Color3.new(1, 1, 1)
ToggleIndex.Font = Enum.Font.GothamBold
ToggleIndex.TextSize = 17
ToggleIndex.BorderSizePixel = 0
Instance.new("UICorner", ToggleIndex)

-- // STATUS DO INDEX
local StatusIndex = Instance.new("TextLabel", Main)
StatusIndex.Size = UDim2.new(0.92, 0, 0, 20)
StatusIndex.Position = UDim2.new(0.04, 0, 0, 474)
StatusIndex.Text = ""
StatusIndex.TextColor3 = Color3.fromRGB(0, 200, 255)
StatusIndex.BackgroundTransparency = 1
StatusIndex.Font = Enum.Font.Gotham
StatusIndex.TextSize = 12
StatusIndex.TextXAlignment = Enum.TextXAlignment.Left

ToggleIndex.MouseButton1Click:Connect(function()
    AutoIndexAtivo = not AutoIndexAtivo
    if AutoIndexAtivo then
        FarmAtivo = false
        Toggle.Text = "AUTO FARM: OFF"
        Toggle.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    end
    ToggleIndex.Text = AutoIndexAtivo and "AUTO INDEX: ON" or "AUTO INDEX: OFF"
    ToggleIndex.BackgroundColor3 = AutoIndexAtivo and Color3.fromRGB(0, 80, 120) or Color3.fromRGB(35, 35, 35)
    if not AutoIndexAtivo then StatusIndex.Text = "" end
end)

-- // ARRASTAR
TopBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
    end
end)
TopBar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- // FUNÇÕES DE BUSCA

local function GetTextoCompleto(prompt)
    local textos = {}
    local parent = prompt.Parent
    if parent then
        table.insert(textos, parent.Name:lower())
        if parent.Parent then
            table.insert(textos, parent.Parent.Name:lower())
        end
    end
    table.insert(textos, (prompt.ObjectText or ""):lower())
    table.insert(textos, (prompt.ActionText or ""):lower())
    if parent then
        for _, desc in ipairs(parent:GetDescendants()) do
            if desc:IsA("TextLabel") or desc:IsA("TextBox") then
                pcall(function() table.insert(textos, desc.Text:lower()) end)
            end
            pcall(function()
                local val = desc:GetAttribute("Mutation")
                if type(val) == "string" then table.insert(textos, val:lower()) end
            end)
        end
        pcall(function()
            local val = parent:GetAttribute("Mutation")
            if type(val) == "string" then table.insert(textos, val:lower()) end
        end)
    end
    return table.concat(textos, " ")
end

local function ContemPalavra(texto, palavra)
    return texto:find("%f[%a]" .. palavra:lower() .. "%f[%A]") ~= nil
end

local function FindPromptDoAlvo()
    for _, obj in pairs(workspace:GetDescendants()) do
        if not obj:IsA("ProximityPrompt") then continue end
        local texto = GetTextoCompleto(obj)
        for alvo in pairs(AlvosSelecionados) do
            if not ContemPalavra(texto, alvo) then continue end
            if next(MutacoesSelecionadas) == nil then return obj end
            for mut in pairs(MutacoesSelecionadas) do
                local variantes = MutTraducao[mut] or {mut}
                for _, v in ipairs(variantes) do
                    if ContemPalavra(texto, v) then return obj end
                end
            end
        end
    end
    return nil
end

local function FarmPrompt(prompt)
    local item = prompt.Parent
    local targetPart = item:IsA("BasePart") and item or item:FindFirstChildWhichIsA("BasePart")
    if not targetPart then return end
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    pcall(function()
        hrp.CFrame = targetPart.CFrame * CFrame.new(0, 2, 0)
        task.wait(0.05)
        prompt.HoldDuration = 0
        fireproximityprompt(prompt)
        task.wait(0.2)
    end)
end

-- // LÊ A INDEX E RETORNA QUAIS BRAINROTS FALTAM
local function GetMissingFromIndex()
    local missing = {}
    local ok, indexFrame = pcall(function()
        return LocalPlayer.PlayerGui.UI.Frames.Index
    end)
    if not ok or not indexFrame then return missing end

    -- clica na aba certa
    pcall(function()
        local btn = indexFrame.Buttons:FindFirstChild(AutoIndexAba)
        if btn then btn.MouseButton1Click:Fire() end
    end)
    task.wait(0.4)

    -- acha o ScrollingFrame da lista
    local scroll = nil
    for _, desc in ipairs(indexFrame:GetDescendants()) do
        if desc:IsA("ScrollingFrame") then
            scroll = desc
            break
        end
    end
    if not scroll then return missing end

    for _, item in ipairs(scroll:GetChildren()) do
        if not item:IsA("Frame") then continue end

        -- pega nome do brainrot
        local brainrotNome = nil
        for _, desc in ipairs(item:GetDescendants()) do
            if desc:IsA("TextLabel") and desc.Name == "Name" then
                brainrotNome = desc.Text:lower()
                break
            end
        end
        -- fallback: qualquer TextLabel com texto sem $ e com mais de 3 chars
        if not brainrotNome then
            for _, desc in ipairs(item:GetDescendants()) do
                if desc:IsA("TextLabel") and desc.Text ~= "" and not desc.Text:find("%$") and #desc.Text > 3 then
                    brainrotNome = desc.Text:lower()
                    break
                end
            end
        end
        if not brainrotNome or brainrotNome == "" then continue end

        -- checa se já foi coletado (procura indicador visível)
        local coletado = false
        for _, desc in ipairs(item:GetDescendants()) do
            local n = desc.Name:lower()
            if n:find("check") or n:find("tick") or n:find("collect") or n:find("owned") or n:find("done") then
                if desc.Visible then
                    coletado = true
                    break
                end
            end
        end

        if not coletado then
            table.insert(missing, brainrotNome)
        end
    end

    return missing
end

-- // LOOP AUTO FARM MANUAL
task.spawn(function()
    while true do
        task.wait(0.1)
        if not FarmAtivo or next(AlvosSelecionados) == nil then continue end
        local prompt = FindPromptDoAlvo()
        if not prompt then continue end
        FarmPrompt(prompt)
    end
end)

-- // LOOP AUTO INDEX
task.spawn(function()
    while true do
        task.wait(0.5)
        if not AutoIndexAtivo then continue end

        local missing = GetMissingFromIndex()

        if #missing == 0 then
            StatusIndex.Text = "Index completa!"
            task.wait(3)
            continue
        end

        local alvoAtual = missing[1]
        StatusIndex.Text = "Faltam: " .. #missing .. " | " .. alvoAtual

        -- procura prompt do brainrot que falta com filtro de mutação/aba
        local promptAchado = nil
        for _, obj in pairs(workspace:GetDescendants()) do
            if not obj:IsA("ProximityPrompt") then continue end
            local texto = GetTextoCompleto(obj)
            if not ContemPalavra(texto, alvoAtual) then continue end

            if AutoIndexAba == "Normal" then
                promptAchado = obj
                break
            else
                local abaLower = AutoIndexAba:lower()
                local variantes = MutTraducao[abaLower] or {abaLower}
                for _, v in ipairs(variantes) do
                    if ContemPalavra(texto, v) then
                        promptAchado = obj
                        break
                    end
                end
                if promptAchado then break end
            end
        end

        if promptAchado then
            FarmPrompt(promptAchado)
        else
            StatusIndex.Text = "Procurando: " .. alvoAtual .. "..."
            task.wait(1)
        end
    end
end)
