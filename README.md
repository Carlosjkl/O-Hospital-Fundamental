-- tabela de médicos
CREATE TABLE medico (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  data_nascimento DATE,
  telefone VARCHAR(30),
  email VARCHAR(150),
  crm VARCHAR(50),
  tipo_medico VARCHAR(20) NOT NULL, -- 'GENERALISTA'|'ESPECIALISTA'|'RESIDENTE'
  origem_registro VARCHAR(50), -- 'planilha','papel','novo_sistema'
  observacoes TEXT,
  created_at TIMESTAMP DEFAULT now()
);

-- especialidades
CREATE TABLE especialidade (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100) NOT NULL UNIQUE,
  descricao TEXT
);

-- ligação N:M entre medico e especialidade
CREATE TABLE medico_especialidade (
  id SERIAL PRIMARY KEY,
  medico_id INT NOT NULL REFERENCES medico(id) ON DELETE CASCADE,
  especialidade_id INT NOT NULL REFERENCES especialidade(id) ON DELETE CASCADE,
  UNIQUE (medico_id, especialidade_id)
);

-- pacientes
CREATE TABLE paciente (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  data_nascimento DATE,
  endereco TEXT,
  telefone VARCHAR(30),
  email VARCHAR(150),
  cpf VARCHAR(14),
  rg VARCHAR(30),
  origem_registro VARCHAR(50),
  created_at TIMESTAMP DEFAULT now()
);

-- convênios
CREATE TABLE convenio (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  cnpj VARCHAR(20),
  tempo_carencia_dias INT DEFAULT 0
);

-- consultas
CREATE TABLE consulta (
  id SERIAL PRIMARY KEY,
  data_hora TIMESTAMP NOT NULL,
  medico_id INT NOT NULL REFERENCES medico(id),
  paciente_id INT NOT NULL REFERENCES paciente(id),
  especialidade_id INT REFERENCES especialidade(id),
  valor_consulta NUMERIC(10,2),
  convenio_id INT REFERENCES convenio(id),
  numero_carteira VARCHAR(100),
  motivo TEXT,
  observacoes TEXT,
  origem_registro VARCHAR(50),
  created_at TIMESTAMP DEFAULT now()
);

-- receita (prescrição) vinculada à consulta
CREATE TABLE receita (
  id SERIAL PRIMARY KEY,
  consulta_id INT NOT NULL REFERENCES consulta(id) ON DELETE CASCADE,
  data_emissao DATE DEFAULT CURRENT_DATE,
  instrucoes_gerais TEXT
);

-- medicamentos (opcional, para normalizar)
CREATE TABLE medicamento (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  concentracao VARCHAR(100),
  forma_farmaceutica VARCHAR(100)
);

-- itens da receita (cada linha: um medicamento + instruções)
CREATE TABLE receita_item (
  id SERIAL PRIMARY KEY,
  receita_id INT NOT NULL REFERENCES receita(id) ON DELETE CASCADE,
  medicamento_id INT REFERENCES medicamento(id),
  nome_medicamento_text VARCHAR(255), -- se quiser gravar o texto exatamente como escrito
  quantidade NUMERIC(10,2),
  unidade VARCHAR(50),
  instrucoes_uso TEXT,
  duracao_dias INT
);
