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

local dragging = false
local dragInput = nil
local dragStart = nil
local startPos = nil

-- // FRAME PRINCIPAL
local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(400, 420)
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
ListBrainrot.Size = UDim2.new(0.92, 0, 0, 130)
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
LabelMut.Position = UDim2.new(0.04, 0, 0, 262)
LabelMut.Text = "MUTAÇÕES (opcional):"
LabelMut.TextColor3 = Color3.fromRGB(0, 255, 127)
LabelMut.BackgroundTransparency = 1
LabelMut.Font = Enum.Font.GothamBold
LabelMut.TextSize = 13
LabelMut.TextXAlignment = Enum.TextXAlignment.Left

-- // LISTA MUTAÇÕES
local ListMut = Instance.new("ScrollingFrame", Main)
ListMut.Size = UDim2.new(0.92, 0, 0, 110)
ListMut.Position = UDim2.new(0.04, 0, 0, 284)
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

-- nomes em portugues e ingles pra garantir
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

-- // COLETA TODO TEXTO RELEVANTE DO OBJETO PAI DO PROMPT
local function GetTextoCompleto(prompt)
    local textos = {}
    local parent = prompt.Parent

    -- nome do pai e avô
    if parent then
        table.insert(textos, parent.Name:lower())
        if parent.Parent then
            table.insert(textos, parent.Parent.Name:lower())
        end
    end

    -- ObjectText e ActionText do próprio prompt
    table.insert(textos, (prompt.ObjectText or ""):lower())
    table.insert(textos, (prompt.ActionText or ""):lower())

    -- textos de labels/billboards filhos do pai
    if parent then
        for _, desc in ipairs(parent:GetDescendants()) do
            if desc:IsA("TextLabel") or desc:IsA("BillboardGui") or desc:IsA("TextBox") then
                table.insert(textos, (desc.Text or ""):lower())
            end
            -- atributos do objeto (ex: Mutation = "Gold")
            local ok, val = pcall(function() return desc:GetAttribute("Mutation") end)
            if ok and type(val) == "string" then
                table.insert(textos, val:lower())
            end
        end
        -- atributos do pai
        local ok, val = pcall(function() return parent:GetAttribute("Mutation") end)
        if ok and type(val) == "string" then
            table.insert(textos, val:lower())
        end
    end

    return table.concat(textos, " ")
end

-- // VERIFICA PALAVRA COMPLETA (evita falso positivo tipo "gold" em "golden")
local function ContemPalavra(texto, palavra)
    return texto:find("%f[%a]" .. palavra .. "%f[%A]") ~= nil
end

-- // BUSCA PROMPT COM FILTRO DE MUTAÇÃO
local function FindPromptDoAlvo()
    for _, obj in pairs(workspace:GetDescendants()) do
        if not obj:IsA("ProximityPrompt") then continue end

        local texto = GetTextoCompleto(obj)

        for alvo in pairs(AlvosSelecionados) do
            if not ContemPalavra(texto, alvo) then continue end

            -- se nenhuma mutação selecionada, pega qualquer um
            if next(MutacoesSelecionadas) == nil then
                return obj
            end

            -- checa se tem a mutação no texto
            for mut in pairs(MutacoesSelecionadas) do
                local variantes = MutTraducao[mut] or {mut}
                for _, v in ipairs(variantes) do
                    if ContemPalavra(texto, v) then
                        return obj
                    end
                end
            end
        end
    end
    return nil
end

-- // LOOP PRINCIPAL
task.spawn(function()
    while true do
        task.wait(0.1)
        if not FarmAtivo or next(AlvosSelecionados) == nil then continue end

        local prompt = FindPromptDoAlvo()
        if not prompt then continue end

        local item = prompt.Parent
        local targetPart = item:IsA("BasePart") and item or item:FindFirstChildWhichIsA("BasePart")
        if not targetPart then continue end

        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then continue end

        pcall(function()
            hrp.CFrame = targetPart.CFrame * CFrame.new(0, 2, 0)
            task.wait(0.05)
            prompt.HoldDuration = 0
            fireproximityprompt(prompt)
            task.wait(0.2)
        end)
    end
end)
